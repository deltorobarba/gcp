
**Authentification**

| Frage | Baustein | Kurz erklärt | Wo du ihn schon hast |
|---|---|---|---|
| **Was denkt und entscheidet?** | **Agent** (ADK) | ein Modell mit Anweisung, das entscheidet, welches Werkzeug es wann benutzt | `agents.ipynb`, `mcp.ipynb` |
| **Wo läuft der Agent?** | **Agent Runtime** (Agent Engine) | Google betreibt den Agent in der Cloud, nicht mehr dein Notebook | `agents.ipynb` (Pipeline deployt) |
| **Wie benutzt ein Agent ein Werkzeug?** | **MCP** | Standard-Schnittstelle zu Tools: Der Agent ruft eine Funktion mit festen Parametern auf und bekommt Daten zurück. Das Tool denkt nicht selbst. | `mcp.ipynb` |
| **Wie arbeitet ein Agent mit einem anderen Agent?** | **A2A** | Standard für Agent-zu-Agent: Ein Agent gibt eine *Aufgabe* in Sprache ab. Der andere Agent hat eigenes Modell und eigene Tools, entscheidet selbst und liefert ein Ergebnis. | `a2a.ipynb` (seit 2026-10-26: ein Rechercheur fragt den BigQuery-Analysten über A2A) |
| **Wer ist der Agent, wenn er etwas aufruft?** | **Identität:** Service Agent, eigenes Dienstkonto oder **Agent Identity (SPIFFE-ID)** | Service Agent: alle Agents im Projekt teilen sich eine Identität. Agent Identity: jeder Agent bekommt seine eigene, ohne Schlüssel. | Service Agent in `agents.ipynb`; Agent Identity in `identity.ipynb` (seit 2026-10-26 deployt, mit eigenem BigQuery-Recht) |
| **Wie beweist er das beim Ziel?** | **Authentifizierung**, je nach Ziel verschieden | Google-API: Access-Token. Cloud Run: ID-Token (OIDC). Fremder Dienst wie GitHub: dessen eigenes Geheimnis (Token), das der **Auth Manager** verwahrt und erst im Moment des Aufrufs herausgibt. | ID-Token in `mcp.ipynb`; API-Key aus Secret Manager in `mcp.ipynb` |
| **Wo findet man alles, und wer kontrolliert?** | **Agent Registry** und **Agent Gateway** | Registry: Verzeichnis aller Agents, MCP-Server und Skills, mit Identität. Gateway: Kontrollpunkt im Datenweg, der Aufrufe durchlässt oder blockt. | Registry in `mcp.ipynb` und `identity.ipynb`; Gateway in `gateway.ipynb` (seit 2026-10-27, mit Model Armor am Eingang) |

Der wichtigste Unterschied ist der zwischen **MCP und A2A**. MCP heißt „Agent benutzt ein Werkzeug“, zum Beispiel „führe diese SQL-Abfrage aus“. A2A heißt „Agent beauftragt einen Kollegen“, zum Beispiel „finde heraus, wie gesund dieses Repository ist“, und der Kollege entscheidet selbst, welche Werkzeuge er dafür benutzt.

**2. Wie die Bausteine zusammenspielen**

```
                         Nutzer
                           │
                    ┌──────▼──────┐        A2A         ┌──────────────┐
  Identität? ─────▶ │  Agent A    │ ─────────────────▶ │  Agent B     │ ◀── eigene Identität
                    │ (Runtime)   │  "erledige Aufgabe" │  (Runtime)   │
                    └──┬────┬─────┘                    └──────┬───────┘
                   MCP │    │ MCP                          MCP │
            ┌──────────┘    └──────────┐                       ▼
            ▼                          ▼                  Fremd-MCP (GitHub)
     eigener MCP-Server         Google-verwalteter        Anmeldung: Token aus
     (Cloud Run, BigQuery)      MCP (Developer            dem Auth Manager
     Anmeldung: ID-Token        Knowledge, BigQuery)
                                Anmeldung: Google-IAM

  Querschnitt:  Agent Registry (wer existiert, wo, als wer)   Agent Gateway (Kontrolle im Datenweg)
```

Es gibt also drei Arten von MCP-Servern:
- **Selbst gebaut**, wie dein BigQuery-Server auf Cloud Run.
- **Von Google betrieben**, wie Developer Knowledge oder der verwaltete BigQuery-MCP.
- **Von Dritten betrieben**, wie GitHub oder Slack.

Je nach Art meldet sich der Agent anders an.

**3. Use Cases zur Auswahl**

| | Use Case | Was du lernst | Neue Bausteine | Stand 2026-11-15 |
|---|---|---|---|---|
| **UC1** | **Deployter Agent mit eigenem MCP-Tool:** dein BigQuery-Analyst läuft auf Agent Runtime statt im Notebook und ruft deinen Cloud-Run-MCP auf | was sich ändert, wenn der Agent nicht mehr *du* ist: ID-Token mit genauer Audience, `run.invoker` für die Agent-Identität, ein Toolset, das sich deployen (pickeln) lässt | Runtime + MCP | erledigt (`mcp.ipynb`, 2026-10-25) |
| **UC2** | **Agent mit Fremd-MCP (GitHub):** ein Agent liest Issues und Repositories über GitHubs MCP-Server | Umgang mit fremden Geheimnissen, Rechte auf Tool-Ebene (`tool_filter`), Prompt Injection durch fremde Inhalte | Fremd-MCP, Secret Manager, später Auth Manager | offen (`github.ipynb`: bisher nur der Plan) |
| **UC3** | **Agent beauftragt Agent (A2A):** zwei Agents auf zwei Engines, z. B. ein Rechercheur, der deinen BigQuery-Analysten als Kollegen fragt | Agent Card, A2A-Server und -Client, Anmeldung zwischen Agents | A2A | erledigt (`a2a.ipynb`, 2026-10-26) |
| **UC4** | **Jeder Agent mit eigener Identität:** Agent Identity statt geteiltem Service Agent, Rechte nur für genau diesen Agent (Least Privilege) | SPIFFE-ID, IAM auf den Principal, Registry zeigt, wer als wer läuft | Agent Identity | erledigt (`identity.ipynb`, 2026-10-26) |
| **UC5** | **Zentrale Kontrolle:** Aufrufe an einen Agent laufen über ein Agent Gateway | Gateway anlegen, binden, Regeln | Agent Gateway | erledigt für den Eingang (`gateway.ipynb`, 2026-10-27); Ausgang nicht umgesetzt |

`claude.ipynb` war im Kern UC1 bis UC4 auf einmal. Deshalb war es so umfangreich und schwer zu durchschauen.

**Meine Empfehlung (Oktober 2026, bis auf UC2 umgesetzt):** zuerst **UC1**. Es baut direkt auf `mcp.ipynb` auf, funktioniert schon jetzt, und es ist die Voraussetzung für alles andere: UC2 bis UC5 setzen deployte Agents voraus. Danach **UC3**, ebenfalls ohne Wartezeit. Nach dem Wechsel weg von der Organisation folgt **UC4**, dann **UC2** mit Auth Manager und zuletzt **UC5**.

**Authentifizierung: Ja, du nutzt schon OIDC, und daran ändert sich nichts**

`credentials.id_token` ist ein OIDC-ID-Token. Cloud Run prüft es bei jedem Aufruf, weil der Dienst mit `--no-allow-unauthenticated` deployt ist. Das bleibt so. Neu ist nur die Frage, *wer* das Token anhängt, wenn der Agent den Server aus der Registry holt.

*Drei Schritte, drei verschiedene Prüfungen:*

| Schritt | Wer ruft wen | Token | Wer prüft | Nötige Rolle |
|---|---|---|---|---|
| 1. Katalog lesen | du (Notebook) → `agentregistry.googleapis.com` | OAuth2-Access-Token (`ya29.…`) aus ADC | die Google-API | Leserecht auf die Agent Registry (als Projekt-Owner vorhanden) |
| 2. MCP-Server aufrufen | du bzw. dein Agent → `bq-mcp-analyst-….run.app` | **OIDC-ID-Token** (JWT, `credentials.id_token`) | Cloud Run | `roles/run.invoker` für den Aufrufer (Projekt-Owner haben das Recht schon über das Projekt) |
| 3. BigQuery abfragen | der Container als `bq-mcp-analyst@…` → `bigquery.googleapis.com` | Access-Token des Dienstkontos (vom Metadata-Server) | BigQuery | `roles/bigquery.jobUser` für das Dienstkonto |

*Access-Token und ID-Token:*

* **Access-Token** (`credentials.token`, beginnt mit `ya29.`): eine Berechtigung für **Google-APIs** wie Vertex AI, BigQuery oder die Agent Registry. Er sagt, *was* der Inhaber darf (Scopes); die API schaut beim Google-Autorisierungsdienst nach, wem er gehört.
* **ID-Token** (`credentials.id_token`, ein JWT `eyJ…`): ein von Google signierter **Identitätsnachweis**. Er enthält, *wer* du bist (`email`), für wen er ausgestellt ist (`aud`, Audience) und wie lange er gilt (`exp`, etwa 1 Stunde). Cloud Run ist keine Google-API, sondern dein eigener Dienst; es will deshalb einen Identitätsnachweis. Cloud Run prüft die Signatur und die Gültigkeit und danach per IAM, ob diese Identität `roles/run.invoker` hat.
* Ein ungültiges Token oder der falsche Typ (Access- statt ID-Token) ergibt `401`; gar kein Token ergibt `403`.

*Warum die Registry einen `header_provider` braucht:*

Bisher hast du die Verbindung selbst gebaut und das Token über `headers=` mitgegeben. 
* Mit der Registry baut ADK die Verbindung in `get_mcp_toolset()` selbst. Der Registry-Eintrag enthält nur URL und Protokoll, **keine Zugangsdaten**; ein Katalog, den viele lesen, soll keine Geheimnisse speichern. 
* Von sich aus hängt ADK Credentials nur an Aufrufe zu `*.googleapis.com`, und das sind Access-Tokens. An `*.run.app` hängt ADK nichts an, und Cloud Run antwortet mit `403`. Deshalb bekommt `AgentRegistry` einen `header_provider`: eine Funktion, die ADK vor jedem MCP-Aufruf ruft und deren Header (`Authorization: Bearer <ID-Token>`) mitgeschickt werden. 
* Sie erneuert das Token, wenn es abgelaufen ist. **Wichtig:** ADK ruft den `header_provider` nur mit einem Agent-Kontext auf, also während ein Agent läuft. 
* Ein direktes `await toolset.get_tools()` außerhalb eines Agents schickt deshalb kein Token, und Cloud Run antwortet mit `403`. 
* Getestet: ohne Kontext 403 (Provider 0-mal gerufen), mit Kontext wird `query_public_dataset` gefunden (Provider 1-mal gerufen).

```diff
- bq_params = StreamableHTTPConnectionParams(
-     url=f"{CLOUD_RUN_URL}/mcp",
-     headers={"Authorization": f"Bearer {credentials.id_token}"},
- )
- tools = MCPToolset(connection_params=bq_params)
+ agent_registry = AgentRegistry(project_id=PROJECT_ID, location=LOCATION,
+                                header_provider=cloud_run_id_token)   # Token kommt pro Aufruf
+ tools = agent_registry.get_mcp_toolset(bq_server["name"])            # URL kommt aus dem Katalog
```

*Später, wenn der Agent deployt ist:* Dein User-ID-Token hat als Audience die Client-ID von gcloud, nicht die Dienst-URL; Cloud Run akzeptiert das für Nutzerkonten. Ein deployter Agent läuft aber als Dienstidentität (Reasoning-Engine-Service-Agent oder Agent Identity). Er muss ein ID-Token mit genau der Dienst-URL als Audience anfordern und braucht selbst `roles/run.invoker`:

```python
import google.oauth2.id_token

def cloud_run_id_token(context) -> dict:
    token = google.oauth2.id_token.fetch_id_token(Request(), CLOUD_RUN_URL)  # Audience = Dienst-URL
    return {"Authorization": f"Bearer {token}"}
```

*Fehlerbilder:*

| Code | Bedeutung |
|---|---|
| `401` | Token vorhanden, aber ungültig oder falscher Typ (Access- statt ID-Token) |
| `403` | gar kein Token (Registry-Weg ohne `header_provider`, oder `get_tools()` ohne Agent-Kontext) oder Token in Ordnung, aber dem Aufrufer fehlt `roles/run.invoker` |
| `FAILED_PRECONDITION … not belong to a permitted customer` beim Invoker-Binding | Org-Policy „Domain Restricted Sharing“ erlaubt keine `@gmail.com`-Mitglieder in IAM-Policies (tritt nur auf, solange das Projekt in einer Organisation liegt); als Projekt-Owner kannst du trotzdem aufrufen |
| `421 Invalid Host header` | Server ohne `host="0.0.0.0"` (DNS-Rebinding-Schutz) |
| `404` auf `/mcp` | es läuft noch die alte SSE-Revision; „Create files“ und „Deploy“ erneut ausführen |

**Was die Registrierung bewirkt**

`gcloud agent-registry services create … --mcp-server-spec-type=no-spec` legt einen *Service* an. Die Registry spiegelt ihn als lesbaren **MCP-Server**-Eintrag (`gcloud agent-registry mcp-servers list`), den Agents und Entwickler finden können. Ein Agent holt sich daraus mit `get_mcp_toolset()` die Verbindung und sieht die Tools mit dem bereinigten Anzeigenamen als Präfix. Zieht der Server um, ändert man nur den Eintrag (`services update`), nicht jeden Agent. Mit `--mcp-server-spec-type=tool-spec` und der Antwort von `tools/list` als Inhalt zeigt die Registry auch die Tools selbst an.

----


Ran search OAuth details in mcp.ipynb and identity.ipynb
Ran list cells in identity.ipynb
Ran search OAuth in samples.md
Ran search OAuth in adk-python.md

**Google-managed OAuth** löst eines der größten Frustrationsthemen im Enterprise-Umfeld: das manuelle Konfigurieren von OAuth-Apps und Secret-Keys für jeden einzelnen Drittanbieter.

Hier ist die genaue Funktionsweise im Detail – vom Unterschied zum alten Weg bis hin zur internen Token-Architektur:

---

### 1. Der alte Weg (Customer-Managed OAuth): Der „Admin-Albtraum“

Bisher (z. B. beim BAP-Connector für Jira) musste der GCP-Admin:
1. In die Atlassian Developer Console (`developer.atlassian.com`) gehen.
2. Eine eigene OAuth 2.0 (3LO) App für das Unternehmen anlegen.
3. Komplexe Redirect-URIs von Google (`https://.../oauth/callback`) eintragen.
4. `client_id` und `client_secret` kopieren und im Google Cloud Secret Manager hinterlegen.
5. Das Problem mit rotierenden Atlassian-Refresh-Tokens managen: Wenn Atlassian Token-Rotation erzwingt oder Scopes ändert, brachen die Verbindungen ab.

---

### 2. Wie Google-managed OAuth funktioniert

Bei **Google-managed OAuth** (`use_google_managed_oauth: true`) übernimmt Google die Rolle des registrierten OAuth-Clients über eine zertifizierte Partnerschaft oder **Dynamic Client Registration (DCR / RFC 7591)**.

Der Ablauf gliedert sich in **4 Schritte**:

```
+-----------------------------------------------------------------------------------+
| 1. ADMIN-EBENE (Einmalig)                                                         |
| Google unterhält eine zertifizierte Multi-Tenant-App direkt bei Atlassian/Slack.   |
| Der GCP-Admin muss KEINE eigene Developer-App, keine Secrets & keine URLs pflegen. |
+-----------------------------------------------------------------------------------+

+-----------------------------------------------------------------------------------+
| 2. USER CONSENT IM CHAT (On-Demand 3LO)                                           |
| User fragt in Gemini: "Zeige mir meine offenen Jira-Bugs."                        |
|                                                                                   |
|  Gemini UI: [ 🔗 Mit Atlassian / Jira verbinden ]                                 |
|                                                                                   |
| User klickt -> Weiterleitung zu Atlassian Auth Screen -> Klick auf "Erlauben"     |
+-----------------------------------------------------------------------------------+

+-----------------------------------------------------------------------------------+
| 3. GOOGLE TOKEN BROKER (Sichere Speicherung)                                      |
| Atlassian schickt Auth-Code an Googles Token Broker.                              |
| Google tauscht ihn gegen Access- & Refresh-Token und speichert diese              |
| verschlüsselt (KMS), strikt isoliert gebunden an die Identität des Nutzers.       |
+-----------------------------------------------------------------------------------+

+-----------------------------------------------------------------------------------+
| 4. MCP RUNTIME & TRANSPARENTE INJECTION                                           |
|                                                                                   |
|  [ LLM ]                                                                          |
|     |  (Entscheidet: Rufe Tool 'jira_search' auf)                                 |
|     v                                                                             |
|  [ Agent Gateway / MCP Client ]                                                   |
|     |  <-- Holt valides Token vom Token Broker (macht autom. Refresh)             |
|     |  Inject: Header "Authorization: Bearer <user_token>"                        |
|     v                                                                             |
|  [ https://mcp.atlassian.com/v1/mcp ]                                              |
|     |  (Führt Query im Kontext des angemeldeten Atlassian-Nutzers aus)            |
|     v                                                                             |
|  [ Jira Cloud ]                                                                   |
+-----------------------------------------------------------------------------------+
```

---

### 3. Die 3 entscheidenden technischen Details

#### A. Zero-Secret Architecture für den Kunden
Weil Google die Client-App bei Atlassian/Slack/Box betreibt, existiert im GCP-Projekt des Kunden **kein Client Secret**, das gestohlen werden oder ablaufen könnte. Der Administrator schaltet den Connector in Gemini Enterprise lediglich per Toggle frei.

#### B. Automatisches Token-Lifecycle-Management
OAuth-Access-Tokens laufen meist nach **60 Minuten** ab. 
* Google betreibt im Hintergrund einen **Managed Token Refresh Service**. 
* Sobald der Agent einen MCP-Call absetzt, prüft der MCP-Client die Gültigkeit des Tokens.
* Ist es abgelaufen, tauscht Google es mittels des gespeicherten Refresh-Tokens im Millisekundenbereich gegen ein neues Token aus.
* Der Endnutzer bekommt davon nichts mit und muss sich **nicht** alle 60 Minuten neu einloggen.

#### C. Striktes Zero-Trust: Das LLM sieht das Token nie!
Ein entscheidender Sicherheitsaspekt:
* Das Sprachmodell (Gemini) erfährt **zu keinem Zeitpunkt**, wie das OAuth-Token lautet.
* Das LLM generiert lediglich den logischen Tool-Call:
  ```json
  {
    "tool": "jira_search_issues",
    "parameters": {"jql": "assignee = currentUser()"}
  }
  ```
* Erst auf der **Transport-Ebene** (im Agent Gateway bzw. MCP Client) schlägt der Token Broker nach: *„Welcher Nutzer führt diesen Request aus?“* (`user@company.com`), holt das passende Token aus dem Tresor und setzt den HTTP-Header:
  ```http
  POST /v1/mcp HTTP/1.1
  Host: mcp.atlassian.com
  Authorization: Bearer eyJhbGciOiJSUzI1...
  ```
* Jira liefert nur genau die Daten zurück, auf die dieser spezifische Nutzer Zugriffsberechtigung hat.

---

### Zusammenfassung

Mit Google-managed OAuth wird Drittanbieter-Zugriff via MCP so einfach wie ein Google-Login:
1. **Für den Admin:** 0 Sekunden Setup für Developer Apps, Scopes und Secrets.
2. **Für den Nutzer:** Einmaliger Klick auf *„Verbinden“* direkt im Chat.
3. **Für die Sicherheit:** Echte Least-Privilege-Sicherheit – jede MCP-Abfrage läuft 100 % im Sicherheitskontext des Nutzers, ohne dass Daten vorher synchronisiert oder kopiert werden müssen.