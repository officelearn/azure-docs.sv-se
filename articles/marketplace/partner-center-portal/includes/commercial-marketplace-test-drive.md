---
title: ta med fil
description: ta med fil
documentationcenter: partner-center-commercial-marketplace
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
ms.date: 08/13/2019
ms.author: dsindona
ms.custom: include file
ms.openlocfilehash: f2446a924ceed37c51779efc9d9e94c0252a2067
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80321819"
---
På fliken **Provkörning** kan du ställa in en demonstration (eller "provkörning") som gör det möjligt för kunder att prova ditt erbjudande innan de bestämmer sig för att köpa det. Läs mer i artikeln [Vad är Provkörning?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive). Om du inte längre vill tillhandahålla en provkörning för ditt erbjudande går du tillbaka till **inställningssidan För Erbjudande** och **avmarkerar Aktivera provkörning**.

### <a name="technical-configuration"></a>Teknisk konfiguration
Följande typer av testenheter är tillgängliga, var och en med sina egna tekniska konfigurationskrav.

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [Logikapp](#technical-configuration-for-logic-app-test-drive)
- [Power BI](#technical-configuration-not-required-for-power-bi-test-drives) (Teknisk konfiguration krävs inte)

#### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Teknisk konfiguration för Azure Resource Manager-testenhet

En distributionsmall som innehåller alla Azure-resurser som utgör din lösning. Produkter som passar det här scenariot använder endast Azure-resurser. Läs mer om hur du konfigurerar en [Azure Resource Manager-testenhet](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).

- **Regioner** (obligatoriskt): För närvarande finns det 26 Azure-stödda regioner där din provkörning kan göras tillgänglig. Vanligtvis vill du göra din provkörning tillgänglig i de regioner där du förutser det största antalet kunder, så att de kan välja den närmaste regionen för bästa prestanda. Du måste se till att din prenumeration tillåts distribuera alla resurser som behövs i var och en av de regioner som du väljer.

- **Instanser**: Välj typ (varmt eller kallt) och antal tillgängliga instanser, som multipliceras med antalet regioner där erbjudandet är tillgängligt.

**Hot**: Den här typen av instans distribueras och väntar på åtkomst per vald region. Kunder kan omedelbart komma åt *Heta* instanser av en provkörning, i stället för att behöva vänta på en distribution. Avvägningen är att dessa instanser alltid körs på din Azure-prenumeration, så att de medför en större drifttidskostnad. Vi rekommenderar starkt att ha *Hot* minst en Hot-instans, eftersom de flesta kunder inte vill vänta på fullständiga distributioner, vilket resulterar i en drop-off i kundanvändningen om ingen *Hot-instans* är tillgänglig.

**Kall**: Den här typen av instans representerar det totala antalet instanser som eventuellt kan distribueras per region. Kalla instanser kräver att hela Test Drive Resource Manager-mallen distribueras när en kund begär provkörningen, så *kalla* instanser är mycket långsammare att läsa in än *heta* instanser. Avvägningen är att du bara behöver betala under hela provkörningen, det körs *inte* alltid på din Azure-prenumeration som med en *Hot-instans.*

- **Azure Resource Manager-mall för testenhet:** Ladda upp ZIP som innehåller din Azure Resource Manager-mall.  Läs mer om hur du skapar en Azure Resource Manager-mall i snabbstartsartikeln [Skapa och distribuera Azure Resource Manager-mallar med hjälp av Azure-portalen](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).

- **Provkörningslängd** (obligatoriskt): Ange hur lång tid provkörningen ska vara aktiv, i antal timmar. Provkörningen avslutas automatiskt när den här tidsperioden är. Denna varaktighet får endast fastställas med ett helt antal timmar (t.ex. "2" timmar, "1,5" är ogiltig).

#### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Teknisk konfiguration för Dynamics 365 provkörning

Microsoft kan ta bort komplexiteten i att konfigurera en provkörning genom att vara värd för och underhålla tillhandahållande och distribution av tjänsten med den här typen av provkörning. Konfigurationen för den här typen av värdbaserade provkörningar är densamma oavsett om testenheten riktar sig till en Business Central-, Customer Engagement- eller Operations-målgrupp.

- **Max samtidiga provkörningar** (krävs): Ange det maximala antalet kunder som kan använda provkörningen samtidigt. Varje samtidig användare förbrukar en Dynamics 365-licens medan provkörningen är aktiv, så du måste se till att du har tillräckligt med licenser tillgängliga för att stödja den högsta angivna gränsen. Rekommenderat värde på 3-5.

- **Provkörningslängd** (obligatoriskt): Ange hur lång tid provkörningen ska vara aktiv genom att definiera antalet timmar. Efter så här många timmar avslutas sessionen och förbrukar inte längre någon av dina licenser. Vi rekommenderar ett värde på 2-24 timmar beroende på komplexiteten i ditt erbjudande. Denna varaktighet får endast fastställas med ett helt antal timmar (t.ex. "2" timmar, "1,5" är ogiltig).  Användaren kan begära en ny session om de får på tid och vill komma åt provkörningen igen.

- **Instans-URL** (obligatoriskt): Webbadressen där kunden börjar sin provkörning. Vanligtvis url:en för din Dynamics 365-instans som kör `https://testdrive.crm.dynamics.com`din app med exempeldata installerade (t.ex. ).

- **Instans webb-API URL** (krävs): Hämta webb-API URL för din Dynamics 365-instans genom att logga in på ditt Microsoft 365-konto och navigera till **Inställningar** \&gt; **Anpassning** \&gt; **Utvecklarresurser** \&gt; **Instanswebb-API (Tjänstrot-URL)**, kopiera webbadressen `https://testdrive.crm.dynamics.com/api/data/v9.0`som finns här (t.ex. ).

- **Rollnamn** (obligatoriskt): Ange det säkerhetsrollnamn som du har definierat i din anpassade Dynamics 365-provkörning. Detta kommer att tilldelas användaren under provkörningen (t.ex. provkörningsroll).

#### <a name="technical-configuration-for-logic-app-test-drive"></a>Teknisk konfiguration för Logic-apptestenhet

Alla anpassade produkter bör använda den här typen av distributionsmall för testkörning som omfattar en mängd komplexa lösningsarkitekturer. Mer information om hur du konfigurerar Logic App-testenheter finns i [Operations](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) and [Customer Engagement](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) på GitHub.

- **Region** (obligatorisk listruta med ett urval): För närvarande finns det 26 Azure-stödda regioner där din provkörning kan göras tillgänglig. Resurserna för logikappen distribueras i den region du väljer. Om logikappen har anpassade resurser som lagras i en viss region kontrollerar du att regionen är markerad här. Det bästa sättet att göra detta är att distribuera din Logic App lokalt på din Azure-prenumeration i portalen och kontrollera att den fungerar korrekt innan du gör det här valet.

- **Max samtidiga provkörningar** (krävs): Ange det maximala antalet kunder som kan använda provkörningen samtidigt. Dessa testenheter har redan distribuerats, vilket gör att kunderna kan komma åt dem direkt utan att vänta på en distribution.

- **Provkörningslängd** (obligatoriskt): Ange hur lång tid provkörningen ska vara aktiv, i antal timmar. Provkörningen avslutas automatiskt när den här tidsperioden är.

- **Namn på Azure-resursgrupp** (obligatoriskt): Ange [azure-resursgruppsnamnet](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) där din Logic App-provkörning sparas.

- **Azure logic app namn** (krävs): Ange namnet på Logic-appen som tilldelar provkörningen till användaren. Den här logic-appen måste sparas i Azure-resursgruppen ovan.

- **Avetablerar logikappnamn** (obligatoriskt): Ange namnet på logic-appen som avetablerar provkörningen när kunden är klar. Den här logic-appen måste sparas i Azure-resursgruppen ovan.

#### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Teknisk konfiguration krävs inte för Power BI-testenheter

Produkter som vill demonstrera ett interaktivt Power BI-visuellt objekt kan använda en inbäddad länk för att dela en specialbyggd instrumentpanel som sin provkörning, ingen ytterligare teknisk konfiguration krävs. Läs mer om hur du konfigurerar Power BI-mallappar.[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)

### <a name="deployment-subscription-details"></a>Information om distributionsprenumeration

Skapa och tillhandahålla en separat, unik Azure-prenumeration för att distribuera testenheten för din räkning. (Krävs inte för Power BI-provkörningar).

- **Azure-prenumerations-ID** (krävs för Azure Resource Manager- och Logic-appar): Ange prenumerations-ID för att bevilja åtkomst till dina Azure-kontotjänster för resursanvändningsrapportering och fakturering. Vi rekommenderar att du överväger att [skapa en separat Azure-prenumeration](https://docs.microsoft.com/azure/billing/billing-create-subscription) som ska användas för testenheter om du inte redan har en. Du hittar ditt Azure-prenumerations-ID genom att logga in på [Azure-portalen](https://portal.azure.com/) och navigera till fliken **Prenumerationer** på menyn till vänster. Om du väljer fliken visas ditt prenumerations-ID (t.ex. "a83645ac-1234-5ab6-6789-1h234g764ghty").

- **Azure AD-klient-ID** (obligatoriskt): Ange ditt Azure Active Directory (AD) [klient-ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in). Om du vill hitta det här ID:t loggar du in på [Azure-portalen](https://portal.azure.com/), väljer fliken Active Directory på menyn, väljer **Egenskaper** och letar sedan efter **katalog-ID-numret** som anges (t.ex. 50c464d3-4930-494c-963c-1e951d15360e). Du kan också slå upp organisationens klient-ID med [https://www.whatismytenantid.com](https://www.whatismytenantid.com)din domännamnsadress på: .

- **Azure AD-klientnamn** (krävs för Dynamic 365): Ange ditt AD-namn (Azure Active Directory). För att hitta det här namnet loggar du in på [Azure-portalen](https://portal.azure.com/), i det övre högra hörnet kommer ditt klientnamn att visas under ditt kontonamn.

- **Azure AD-app-ID** (obligatoriskt): Ange ditt AZURE Active Directory (AD) [program-ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in). Om du vill hitta det här ID:t loggar du in på [Azure-portalen](https://portal.azure.com/), väljer fliken Active Directory på menyn till vänster, väljer **Appregistreringar**och letar sedan efter **program-ID-numret** som anges (t.ex. 50c464d3-4930-494c-963c-1e951d15360e).

- **Azure AD-appklienthemma** (obligatoriskt): Ange din [Azure AD-programklienthemlighet](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets). Logga in på [Azure-portalen](https://portal.azure.com/)om du vill hitta det här värdet . Välj fliken **Azure Active Directory** på den vänstra menyn, välj Appregistreringar och välj sedan din provkörningsapp. **App registrations** Välj sedan **Certifikat och hemligheter**, välj Ny **klienthemlighet**, ange en beskrivning, välj **Aldrig** under Upphör **att gälla**och välj sedan Lägg **till**. Se till att kopiera ner värdet. (Navigera inte bort från sidan innan du gör detta, annars har du inte tillgång till värdet.)

Kom ihåg att **spara** innan du går vidare till nästa avsnitt!

### <a name="test-drive-listings-optional"></a>Provkörningslistor (valfritt)

Alternativet **Test drive-listor** som finns under fliken **Provkörning** visar de språk (och marknader) där provkörningen är tillgänglig, för närvarande engelska (USA) är den enda tillgängliga platsen. Dessutom visar den här sidan status för den språkspecifika listan och datum/tid då den lades till. Du måste definiera provkörningsinformation (beskrivning, bruksanvisning, videor, etc.) för varje språk / marknad.

- **Beskrivning** (obligatoriskt): Beskriv din provkörning, vad som visas, mål för användaren att experimentera med, funktioner att utforska och all relevant information som hjälper användaren att avgöra om du vill hämta ditt erbjudande. Upp till 3 000 tecken text kan anges i det här fältet. 

- **Åtkomstinformation** (krävs för Azure Resource Manager och Logic testenheter): Förklara vad en kund behöver veta för att komma åt och använda den här testenheten. Gå igenom ett scenario för att använda ditt erbjudande och exakt vad kunden bör veta för att komma åt funktioner under hela provkörningen. Upp till 10 000 tecken text kan anges i det här fältet.

- **Användarhandbok** (krävs): En djupgående genomgång av din provkörningsupplevelse. Användarhandboken bör omfatta exakt vad du vill att kunden ska vinna på att uppleva provkörningen och fungera som referens för eventuella frågor som de kan ha. Filen måste vara i PDF-format och namnges (max 255 tecken) efter uppladdning.

- **Videor: Lägg till videor** (valfritt): Videor kan laddas upp till YouTube eller Vimeo och refereras här med en länk och miniatyrbild (533 x 324 pixlar) så att en kund kan visa en genomgång av information för att hjälpa dem att bättre förstå provkörningen, inklusive hur du framgångsrikt använder funktionerna i ditt erbjudande och förstår scenarier som belyser deras fördelar.
  - **Namn** (obligatoriskt)
  - **URL (endast YouTube eller Vimeo)** (obligatoriskt)
  - **Miniatyr (533 x 324px):** Bildfilen måste vara i PNG-format.

Välj **Spara** när du har slutfört dessa fält.
