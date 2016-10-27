<properties
    pageTitle="Använda Outlook i Azure RemoteApp | Microsoft Azure" 
    description="Information om hur du konfigurerar och använder Outlook i Azure RemoteApp | Microsoft Azure"
    services="remoteapp"
    documentationCenter=""
    authors="pavithir"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="08/15/2016"
    ms.author="elizapo" />


# <a name="using-microsoft-outlook-in-azure-remoteapp"></a>Använda Microsoft Outlook i Azure RemoteApp

> [AZURE.IMPORTANT]
> Azure RemoteApp upphör att gälla. Läs [meddelandet](https://go.microsoft.com/fwlink/?linkid=821148) för mer information.

Azure RemoteApp stöder Microsoft Outlook O365. Läs mer om hur [Office fungerar i Azure RemoteApp](remoteapp-officesubscription.md). Det finns några rekommenderade inställningar för Outlook när det används i Azure RemoteApp.

## <a name="cached-mode"></a>Cachelagrat läge
Cachelagrat läge är en rekommenderad konfiguration när du använder Outlook i Azure RemoteApp. När du konfigurerar ett Outlook 2013-konto att använda cachelagrat Exchange-läge arbetar Outlook 2013 från en lokal kopia av användarens Microsoft Exchange-postlåda som är lagrad i en offline-datafil (OST-fil) på datorn, tillsammans med offline-adressboken (OAB). Den cachelagrade postlådan och OAB uppdateras regelbundet från O365-tjänsten. Läs mer om [skillnaderna mellan cachelagrat läge och onlineläge](https://technet.microsoft.com/library/jj683103.aspx).

Användaren kan välja **cachelagrat Exchange-läge** eller **onlineläge** under kontoinstallationen eller genom att ändra inställningarna för kontot. Du kan också distribuera något av dessa lägen genom att använda verktyget Office-anpassning eller grupprincipen.  

Läs [steg för steg-instruktionerna om hur du aktiverar cachelagrat läge](https://technet.microsoft.com/library/c6f4cad9-c918-420e-bab3-8b49e1885034#proc).

## <a name="search"></a>Söka
Sökningen i Outlook har begränsningar i Azure RemoteApp. Azure RemoteApp använder delade virtuella datorer för att hantera användarsessioner. Sökindexeringen beror på dator-ID:t, som skiljer sig mellan olika virtuella datorer. Det är möjligt att en användare dirigeras till en ny virtuell dator varje gång de loggar in på Azure RemoteApp. Det betyder att om vi aktiverar lokal sökning körs indexeraren varje gång dator-ID:t ändras (när användaren arbetar från en annan virtuell dator). Beroende på hur stor .OST-filen är kan det ta lång tid för indexeraren att slutföra, vilket betyder att den använder upp resurser som krävs för andra appar. Sökningen blir inte bara långsam, den kanske inte heller ger resultat. Det går att komma runt detta genom att använda kontoprofil med onlineläge, men den allmänna prestandan skulle bli sämre på grund av brist på lokalt cacheminne (mer information om skillnaden mellan cacheläge och onlineläge kommer du till via länken ovan). Tyvärr går det inte att inaktivera indexerad/lokal sökning, och onlinesökning kan inte aktiveras som standard i Outlook 2013.



<!--HONumber=Oct16_HO3-->


