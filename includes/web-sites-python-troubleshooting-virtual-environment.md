Distributions-skriptet hoppar över att skapa den virtuella miljön i Azure om den identifierar att det redan finns en kompatibel virtuell miljö.  Det kan påskynda distributionen avsevärt.  Paket som redan är installerade kommer att hoppas över av pip.

I vissa situationer kan du vilja framtvinga borttagning av den virtuella miljön.  Det vill du göra om du bestämmer du bestämmer dig för att inkludera en virtuell miljö som en del av ditt centrallager.  Det är också möjligt att du vill göra det om du behöver bli av med vissa paket, eller testa ändringar i requirements.txt.

Det finns några alternativ för att hantera den befintliga virtuella miljön i Azure:

### Alternativ 1: Använd FTP

Anslut till servern med en FTP-klient så kan du ta bort env-mappen.  Observera att vissa FTP-klienter (till exempel webbläsare) bara erbjuder läsning och inte tillåter att du tar bort mappar så du får se till att använda en FTP-klient med den funktionen.  FTP-värdnamnet och användaren, visas i din webbapps blad på [Azure-portalen](https://portal.azure.com).

### Alternativ 2: Växla körning

Det här är ett alternativ som drar nytta av det faktum att distributionsskriptet tar bort env-mappen när den inte matchar den önskade versionen av Python.  Det här tar effektivt bort den befintliga miljön och skapar en ny sådan.

1. Växla till en annan version av Python (via runtime.txt eller **Programinställningar**-bladet i Azure Portal)
1. git-pusha några ändringar (ignorera eventuella pip-installationsfel)
1. Växla tillbaka till den första versionen av Python
1. git-pusha några ändringar igen

### Alternativ 3: Anpassa distributionsskriptet

Om du har anpassat distributionsskriptet, kan du ändra koden i deploy.cmd för att tvinga den att ta bort env-mappen.


<!--HONumber=Sep16_HO3-->


