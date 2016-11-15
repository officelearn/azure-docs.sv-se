---
title: "Vad är Azure RemoteApp? | Microsoft Docs"
description: "Här får du lära dig hur du kan dela appar och resurser mellan olika enheter med Azure RemoteApp."
services: remoteapp
documentationcenter: 
author: lizap
manager: mbaldwin
editor: 
ms.assetid: d7a8a311-e70a-4463-ac85-c7f62c500921
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/15/2016
ms.author: elizapo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 95b4e6914de787bb2c25527ce9635f872b4746a6


---
# <a name="what-is-azure-remoteapp"></a>Vad är Azure RemoteApp?
> [!IMPORTANT]
> Azure RemoteApp upphör att gälla. Läs [meddelandet](https://go.microsoft.com/fwlink/?linkid=821148) för mer information.
> 
> 

Med Azure RemoteApp får du genom fjärrskrivbordstjänster samma funktioner i Azure som du får med ett lokalt Microsoft RemoteApp-program. Med Azure RemoteApp kan användarna få säker fjärråtkomst till program från olika typer av enheter. På Azure RemoteApp körs tillfälliga terminalserversessioner i molnet, och du kan använda dem och dela dem med användarna.

Med Azure RemoteApp kan du dela program och resurser med användarna på nästan vilken typ av enhet som helst. Dina program ligger hos oss i molnet, vilket innebär att vi tar hand om all maskinvara och nödvändig skalning. Allt du behöver göra är att överföra programmen som du vill dela till oss i molnet och sedan kan användarna använda dem. [Användarna kan använda sina egna enheter](remoteapp-clients.md), samtidigt som du administrerar allt via Azure Portal. Du kan även välja att företagets inloggningsuppgifter ska användas så att säkerheten för program och data kan säkerställas.

Fortsätt läsa för mer information om Azure RemoteApp, eller [prova nu](https://azure.microsoft.com/services/remoteapp/) om du redan vet vad du vill.

Har du frågor om Azure RemoteApp? Läs våra [vanliga frågor och svar](remoteapp-faq.md).

Azure RemoteApp är en del av [Microsofts infrastruktur för virtuella skrivbord](http://www.microsoft.com/server-cloud/products/virtual-desktop-infrastructure/explore.aspx).

**Nyhet!** Vill du veta mer om Azure RemoteApp? Eller vill du prova Azure RemoteApp i större skala? Varje vecka anordnar vi ett [webbseminarium där du kan fråga våra experter](https://azureinfo.microsoft.com/AzureRemoteAppAskTheExperts-Registration-Page.html?ls=Website).

## <a name="azure-remoteapp-collections"></a>Azure RemoteApp-samlingar
Det finns två typer av [Azure RemoteApp](remoteapp-collections.md)-samlingar: moln- och hybridsamlingar.

* Med en **molnsamling** ligger programmen i molnet och alla data för programmen lagras i molnet. Användarna kan öppna programmen genom att logga in på sitt Microsoftkonto eller med sina inloggningsuppgifter till företaget, synkroniserat eller federerat med Azure Active Directory.
  
    Du bör välja molnsamling om det inte behövs någon anslutning till en resurs i företagets privata nät, (till exempel via en VPN-enhet) för att använda programmet som du vill dela. Om det behövs resurser på Internet, OneDrive eller Azure för att använda programmet fungerar det bra med molnsamling. En sådan samling går även snabbast att skapa.
* Med en **hybridsamling** ligger och lagras data i Azuremolnet, men användarna har även åtkomst till data och resurser i ert lokala nät. Användarna kan öppna programmen genom att logga in med sina inloggningsuppgifter till företaget , synkroniserat eller federerat med Azure Active Directory.
  
    Du bör välja hybridsamling om det behövs anslutning till resurser i företagets privata nät, till exempel om det behövs åtkomst till något av följande för att kunna använda programmet:
  
  * filservrar som finns i ert intranät
  * Quicken
  * databaser bakom en brandvägg
    
    Den här lösningen passar bättre för stora företag med mycket resurser i sitt privata nät som inte kan flyttas till molnet.

Det är olika alternativ för de olika samlingarna, till exempel nätverk, så fundera på [vilken samling](remoteapp-collections.md) som passar bäst för ditt företag. 

### <a name="updating-your-collection"></a>Uppdatera samlingen
En av de största skillnaderna mellan hybrid- och molnsamlingar är hur programuppdateringar hanteras. Om du använder en molnsamling med den förinstallerade Office 365 ProPlus- eller Office 2013-avbildningen behöver du inte bekymra dig om uppdateringar. Underhåll sker automatiskt och både program och operativsystemet uppdateras regelbundet.

I hybridsamlingar, liksom i molnsamlingar med egeninställd mallavbildning, är det du som har ansvar för underhållet av avbildningen och programmen. För domänanslutna avbildningar kan du styra uppdateringarna med verktyg som Windows Update, grupprinciper eller System Center.

När du har uppdaterat en egeninställd mallavbildning överför du den nya avbildningen till Azuremolnet och uppdaterar sedan samlingen så att den nya avbildningen används. (Det kan du göra via sidan **Quick Start (Snabbstart)** eller instrumentpanelen för Azure RemoteApp.)

Se artikeln om att [uppdatera samlingar ](remoteapp-update.md) för mer information.

## <a name="supported-remoteapp-clients"></a>Kompatibla RemoteApp-klienter
Azure RemoteApp kan användas i RemoteApp-klientappar för Windows och Windows RT, liksom Microsofts fjärrskrivbordsappar för Mac, iOS och Android. Användarna kan öppna de nya Azure RemoteApp-programmen via de här apparna på sina mobila eller stationära datorenheter.

Se artikeln om att [öppna program i Azure RemoteApp](remoteapp-clients.md) för mer information om klienterna.

## <a name="next-steps"></a>Nästa steg
Sätt igång och prova! I de här artiklarna får du hjälp med att komma igång med Azure RemoteApp:

* [Vilken typ av samling behöver du för Azure RemoteApp?](remoteapp-collections.md)
* [Skapa en Azure RemoteApp-avbildning](remoteapp-imageoptions.md)
* [Så här skapar du en molnsamling av Azure RemoteApp](remoteapp-create-cloud-deployment.md)
* [Så här skapar du en hybridsamling av Azure RemoteApp](remoteapp-create-hybrid-deployment.md)
* [Så här fungerar det med licensiering i Azure RemoteApp](remoteapp-licensing.md)
* [Tips på hur du arbetar med Azure RemoteApp](remoteapp-bestpractices.md)
* [Vanliga frågor och svar om Azure RemoteApp](remoteapp-faq.md)

### <a name="help-us-help-you"></a>Hjälp oss att hjälpa
Visste du att du förutom att betygsätta den här artikeln och skriva kommentarer nedan även kan göra ändringar i själva artikeln? Saknar du något i artikeln? Är det något som är fel? Har jag skrivit något som helt enkelt bara är förvirrande? Då kan du gå tillbaka till början av sidan och klicka på **Edit on GitHub (Redigera på GitHub)** eller **Edit (Redigera)** och ändra i texten. Ändringarna skickas till oss och när vi har godkänt dem kan du se dina ändringar och förbättringar här.




<!--HONumber=Nov16_HO2-->


