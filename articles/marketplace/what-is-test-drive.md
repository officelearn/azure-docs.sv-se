---
title: Vad är en provkörning? Microsoft Commercial Marketplace
description: Förklaring av funktionen för Marketplace-testenhet
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 06/19/2020
ms.openlocfilehash: bd3b77f80a414dd3db1d5106929fa0e215e34c1f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "88121875"
---
# <a name="what-is-a-test-drive"></a>Vad är en provkörning?

En testen het är ett bra sätt att presentera ditt erbjudande för potentiella kunder genom att ge dem möjlighet att *prova innan du köper*, vilket resulterar i ökad konvertering och generering av mycket kvalificerade leads. En testen het ger din produkt liv i ett verkligt implementerings scenario samtidigt som du genererar hög kvalificerade leads.

Test enheter är hanterade instanser som distribuerar din lösning eller program på begäran för kunder som begär det. När en test enhets instans har tilldelats är den tillgänglig för användning under en angiven tids period och tas sedan bort för att skapa utrymme för en annan kund.

Som utgivare hanterar och konfigurerar du inställningarna för test enheten i Partner Center. Teknisk konfigurations information varierar beroende på vilken typ av erbjudande du arbetar med. Detaljerad vägledning finns i länken under [Nästa steg](#next-step) i slutet av det här avsnittet.

Potentiella kunder identifierar test enheten på den kommersiella marknaden. De ger sina kontakt uppgifter och samtycker till ditt erbjudandes villkor och sekretess policy och får sedan till gång till förkonfigurerad miljö för att testa den under en viss tids period. Kunderna får en praktisk, själv guidad utvärderings version av produktens viktiga funktioner och fördelar och du får en värdefull lead.

## <a name="how-does-it-work"></a>Hur fungerar det?

Som utgivare hanterar och konfigurerar du inställningarna för test enheten i Partner Center. Efter installationen blir den en hanterad instans som kommer att distribueras på begäran för kunden som begär det. När en test enhets instans har tilldelats är den tillgänglig för användning under en angiven tids period och tas sedan bort för att skapa utrymme för en annan kund.

## <a name="types-of-test-drives"></a>Typer av test enheter

Det finns olika test enheter tillgängliga på den kommersiella marknads platsen för utvalda erbjudanden beroende på vilken typ av produkt, scenario och Marketplace du är på:

- Azure Resource Manager
- Värdbaserad testen het
    - Dynamics 365 för Business Central
    - Dynamics 365 för Customer Engagement
    - Dynamics 365 for Operations
- Logikapp
- Power BI

Mer information om hur du konfigurerar en av dessa test enheter finns i länken under [Nästa steg](#next-step) i slutet av det här avsnittet.

### <a name="azure-resource-manager-test-drive"></a>Azure Resource Manager testen het

Den här distributions mal len innehåller alla Azure-resurser som utgör din lösning. Produkter som passar det här scenariot använder bara Azure-resurser. Den Azure Resource Manager test enheten är tillgänglig för dessa erbjudande typer: 

- Azure-program
- Dynamics 365 för Business Central
- Dynamics 365 för kund engagemang & PowerApps
- Dynamics 365 for Operations
- SaaS
- Virtuella datorer

>[!NOTE]
>Detta är det enda alternativet test enhet för erbjudanden för virtuella datorer och Azure-program.

### <a name="hosted-test-drive"></a>Värdbaserad testen het

En värdbaserad testen het tar bort komplexiteten i installationen genom att låta Microsoft Host och underhålla tjänsten som utför test enhetens användar etablering, distribution och avetablering. Om du har ett erbjudande på Microsoft AppSource, kan du bygga din test enhet för att ansluta till en Dynamics AX/Azure-instans eller någon annan resurs utöver bara Azure. Använd den här typen för AppSource-erbjudanden för att ansluta till dessa Dynamics 365-erbjudanden:

- Använd [Dynamics 365 for Business Central](partner-center-portal/create-new-operations-offer.md) för företagets centrala företags resurs planerings system som ekonomi, drift, leverans kedja och CRM.
- Använd [Dynamics 365 för kund engagemang](partner-center-portal/create-new-customer-engagement-offer.md) för ett kund engagemang system, till exempel Sales, service, Project service och Field service.
- Använd [Dynamics 365 för åtgärder](partner-center-portal/create-new-operations-offer.md) för ekonomi-och drift företags resurs planerings system, till exempel ekonomi, drift och tillverkning, leverans kedja.

### <a name="logic-app-test-drive"></a>Test enhet för Logic app

Den här typen av test enhet är inte Microsoft-värd. Använd den för att ansluta till ett erbjudande för Dynamics 365 eller en annan anpassad resurs.

### <a name="power-bi-test-drive"></a>Power BI testen het

Detta är bara en inbäddad länk till en anpassad instrument panel. En produkt som endast visar ett interaktivt Power BI visuellt objekt bör använda den här typen av test enhet.

## <a name="transforming-examples"></a>Transformera exempel

Processen att aktivera en arkitektur av resurser till en test enhet kan vara avskräckande. Kolla i följande exempel på hur du bäst omformar aktuella arkitekturer.

[Transformera en webbplatsmall till en test enhet](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Website-Deployment-Template-for-Test-Drive)

[Transformera en mall för virtuella datorer till en test enhet](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Virtual-Machine-Deployment-Template-for-Test-Drive)

[Omvandla en befintlig Resource Manager-mall till en test enhet](https://github.com/Azure/AzureTestDrive/wiki/Deploying-Existing-Solutions)

## <a name="generate-leads-from-your-test-drive"></a>Generera leads från test enheten

En extern Marketplace-testenhet är ett utmärkt verktyg för marknads förare. Vi rekommenderar att du införlivar det i din marknads verksamhet när du startar för att generera fler leads för ditt företag. Mer detaljerad information finns i [kund leads från ditt kommersiella Marketplace-erbjudande](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads.md).

Om du stänger ett avtal med en testen het måste du registrera det på [Microsoft partner Sales Connect](https://support.microsoft.com/help/3155788/getting-started-with-microsoft-partner-sales-connect). Vi skulle också älska att höra om din kund vinner där en testenhet spelade en roll.

## <a name="other-resources"></a>Andra resurser

Ytterligare resurser för test enheter:

- [Rekommenderade tekniska metoder](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Översikt](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (PDF, se till att blockering av popup-fönster är inaktiverat)

## <a name="next-step"></a>Nästa steg

- [Teknisk konfiguration för provkörning](test-drive-technical-configuration.md)
