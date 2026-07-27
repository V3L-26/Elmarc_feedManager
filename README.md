# Elmarc Feed manager

Eén-bestands webapp (`index.html`) voor `feed.elmarc.nl`. Geen build-stap
nodig: dit bestand kun je direct op je webserver zetten (bijv. als root van
de hosting voor dat (sub)domein) en het werkt meteen.

## Wat erin zit

- Dashboard met linker in-/uitklapbaar menu, Elmarc-logo, disclaimer en
  taalkeuze (NL/EN/DE, live vertaald).
- **Expert**-pagina: feed actief/inactief zetten, feed handmatig draaien,
  artikelenoverzicht met een aan/uit-schuifje per SKU.
- **Monta Stock**-pagina: voorraad per magazijn (Elmarc/Elmarc_B2B) en
  gecombineerd, uit de Supabase-cache.
- **Koppelingen**-menu (uitklapbaar) met daaronder:
  - **Monta WMS**: gebruikersnaam/wachtwoord bij een vaste API-URL,
    inclusief opslaan en verbinding testen.
  - **Nieuwe koppeling**: maak zelf extra koppelingen aan met een eigen
    naam en API-URL. Elke koppeling krijgt hetzelfde instellingenblad als
    Monta WMS (API-URL, gebruikersnaam, wachtwoord, opslaan, testen).

## Backend

De Monta-instellingen (gebruikersnaam + wachtwoord) worden **niet** in dit
bestand of in de browser opgeslagen. De pagina praat met een Supabase-project
dat los staat van al je andere Supabase-projecten:

- Project: **Elmarc Feed manager** (project-ref `hftenvysinatgeoutvml`,
  regio eu-north-1)
- Database: schema `feed_manager`, tabellen `monta_settings` en
  `custom_connections` (alleen gebruikersnaam + verwijzing naar de
  Vault-secret, nooit het wachtwoord zelf in leesbare vorm)
- Wachtwoord-opslag: Supabase Vault (versleuteld)
- Edge Functions:
  - `monta-settings` — opslaan, status ophalen en de verbindingstest met
    de echte Monta WMS API (`https://api-v6.monta.nl/`)
  - `monta-stock` / `monta-articles` — voorraad- en artikelcache
  - `connections-settings` — dezelfde acties voor de zelf aangemaakte
    koppelingen, tegen hun eigen API-URL

De database-functies die wachtwoorden ontsleutelen of gegevens wegschrijven
zijn bewust **niet** aanroepbaar met de publieke anon-key: alleen de Edge
Functions (met de service-role) mogen erbij. Voeg bij een nieuwe RPC dus
altijd een `revoke execute ... from anon, authenticated, public` toe.

Voor het beheer van dit Supabase-project (bijv. het wachtwoord handmatig
resetten, logs bekijken) log je in op supabase.com met het account waarmee
dit project is aangemaakt.

## Deployen

1. Zet `index.html` op je server voor `feed.elmarc.nl` (via je hosting-panel,
   FTP/SFTP, of een deploy-actie vanuit deze GitHub-repo als je dat al hebt
   ingericht).
2. Zorg dat HTTPS actief is op dat domein — de pagina praat over HTTPS met
   Supabase, dus de pagina zelf moet ook over HTTPS bereikbaar zijn (anders
   blokkeren browsers de aanroepen als "mixed content").
3. Verder is er niets te installeren of te configureren: alle instellingen
   worden via de "Instellingen"-pagina in de app zelf beheerd.

## Volgende stappen

Dit is de eerste live versie. De daadwerkelijke koppeling met Expert
(voorraadstanden ophalen bij Monta en doorsturen) en het artikeloverzicht
met aan/uit-vinkjes per SKU volgen in een latere stap.
