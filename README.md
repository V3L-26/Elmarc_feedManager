# Elmarc Feed manager

Eén-bestands webapp (`index.html`) voor `feed.elmarc.nl`. Geen build-stap
nodig: dit bestand kun je direct op je webserver zetten (bijv. als root van
de hosting voor dat (sub)domein) en het werkt meteen.

## Wat erin zit

- Dashboard met linker in-/uitklapbaar menu, Elmarc-logo, disclaimer en
  taalkeuze (NL/EN/DE, live vertaald).
- **Expert**-pagina: feed actief/inactief zetten, feed handmatig draaien,
  artikeloverzicht (nog "binnenkort").
- **Instellingen**-pagina: MontaWMS-koppeling (gebruikersnaam/wachtwoord,
  vaste API-URL) inclusief opslaan en verbinding testen.

## Backend

De Monta-instellingen (gebruikersnaam + wachtwoord) worden **niet** in dit
bestand of in de browser opgeslagen. De pagina praat met een Supabase-project
dat los staat van al je andere Supabase-projecten:

- Project: **Elmarc Feed manager** (project-ref `hftenvysinatgeoutvml`,
  regio eu-north-1)
- Database: schema `feed_manager`, tabel `monta_settings` (alleen
  gebruikersnaam + verwijzing naar de Vault-secret, nooit het wachtwoord
  zelf in leesbare vorm)
- Wachtwoord-opslag: Supabase Vault (versleuteld)
- Edge Function: `monta-settings` — verzorgt opslaan, status ophalen en de
  verbindingstest met de echte Monta WMS API (`https://api-v6.monta.nl/`)

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
