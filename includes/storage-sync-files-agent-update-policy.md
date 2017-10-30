Uppdateringar av Azure File Sync-agenten släpps regelbundet för att lägga till nya funktioner och för att åtgärda eventuella problem som hittas. Vi rekommenderar att du aktiverar Microsoft Update för att hämta alla uppdateringar för Azure File Sync-agenten allteftersom vi släpper dem. Vi förstår dock att vissa organisationer vill ha en strikt kontroll och testning av uppdateringar. För distributioner med äldre versioner av Azure File Sync-agenten:

- Storage Sync-tjänsten följer den tidigare huvudversionen i tre månader efter den första versionen av en ny huvudversion. Version 1. \* till exempel, skulle stödjas av Storage Sync-tjänsten tills tre månader efter att version 2.\* har släppts.
- Efter att tre månader har gått, börjar Storage Sync-tjänsten att blockera registrerade servrar som använder den utgångna versionen från att synkronisera med deras synkroniseringsgrupper.
- Inom tre månader från en tidigare huvudversion, kommer alla snabbkorrigeringar att enbart gå till den aktuella huvudversionen.

> [!Note]  
> Vi meddelar dig via popup-meddelande i Azure Portal om du använder en version av Azure File Sync som upphör att gälla inom de kommande tre månaderna.