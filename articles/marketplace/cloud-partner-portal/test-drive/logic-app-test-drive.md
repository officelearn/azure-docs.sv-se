---
title: Testenhet för logikapp | Azure Marketplace
description: Förklarar hur du skapar sin testenhet som ansluter till en Dynamics AX/CRM-instans eller någon annan resurs utöver bara Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 766f893d71ca0830fe8b69c50145603c6544cc3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278390"
---
<a name="logic-app-test-drive"></a>Testenhet för logikapp
====================

Den här artikeln är för utgivare som har sitt erbjudande på AppSource och vill bygga sin testenhet som ansluter till en Dynamics AX/CRM-instans eller någon annan resurs utöver bara Azure.

<a name="how-to-build-a-logic-app-test-drive"></a>Så här skapar du en Logic App Test Drive
-----------------------------------

Test Drive-dokumentation för Logic App Test Drives finns fortfarande på GitHub for [Operations](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) and [Customer Engagement](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app), gå dit för att läsa mer.

<a name="how-to-publish-a-test-drive"></a>Så här publicerar du en provkörning
---------------------------

Nu när du har byggt testenheten går det här avsnittet igenom vart och ett av de fält som krävs för att du ska kunna publicera testenheten.

![Aktivera testenhetsfunktionen](./media/azure-resource-manager-test-drive/howtopub1.png)

Det första och viktigaste fältet är att växla om du vill testa formuläret med alla obligatoriska fält som du kan fylla i. När du väljer **Nej** inaktiveras formuläret och om du publicerar om med testenheten inaktiverad tas testenheten bort från produktionen.

*Om*det finns några testenheter som aktivt används av användare fortsätter dessa testenheter att köras tills sessionen går ut.

### <a name="details"></a>Information

Nästa avsnitt att fylla i är information om ditt Test Drive-erbjudande.

![Information om testdrivrutin](./media/azure-resource-manager-test-drive/howtopub2.png)

**Beskrivning -** *[Obligatoriskt fält]* Det är här du skriver huvudbeskrivningen om vad som finns på provkörningen. Kunden kommer hit för att läsa vilka scenarier din testkörning kommer att täcka om din produkt. 

**Användarhandbok -** *[Obligatoriskt fält]* Detta är den djupgående genomgången av din testkörningsupplevelse. Kunden kommer att öppna detta och kan gå igenom exakt vad du vill att de ska göra under hela sin provkörning. Det är viktigt att detta innehåll är lätt att förstå och följa! (Måste vara en PDF-fil)

**Test Drive Demo Video -** \[Rekommenderas\] i likhet med bruksanvisningen, är det bäst att inkludera en video tutorial av din Test Drive erfarenhet. Kunden kommer att titta på detta före eller under sin provkörning och kan gå igenom exakt vad du vill att de ska göra under hela sin provkörning. Det är viktigt att detta innehåll är lätt att förstå och följa!

- **Namn** - Titel på din video
- **Länk** - Måste vara en inbäddad webbadress från YouTube eller Vimeo. Exempel på hur du får den inbäddade webbadressen finns nedan:
- **Miniatyr** - Måste vara en högkvalitativ bild (533x324) pixlar. Det rekommenderas att ta en skärmdump av någon del av din Test Drive-upplevelse här.

Nedan visas hur dessa fält visas för din kund under deras provkörningsupplevelse.

![Test drive-fält ser ut och känns](./media/azure-resource-manager-test-drive/howtopub4.png)

### <a name="technical-configuration"></a>Teknisk konfiguration

Nästa avsnitt som ska fyllas i är där du konfigurerar din Test Drive Logic App och definiera hur specifikt dina Test Drive-instanser fungerar.

![Teknisk konfiguration för testkörning](./media/azure-resource-manager-test-drive/howtopub5_logicapp.png)

- **Region** - *[Obligatoriskt fält]* Den region du väljer är där du väljer var dina Resurser för Test Drive Logic App distribueras i.

    *Anm.:* Om logikappen har anpassade resurser som lagras i en region kontrollerar du att regionen är markerad här. Det bästa sättet att göra detta är att **distribuera din Logic App lokalt på din Azure-prenumeration i portalen och kontrollera att det fungerar** innan du skriver in den här.

- **Maximalt antal samtidiga provenheter** - *[Obligatoriskt fält]* Antal testenhetsinstanser som redan har distribuerats och väntar på åtkomst per vald region. Kunder kan omedelbart komma åt dessa testenheter i stället för att behöva vänta på en distribution.

    *Anm.:* Om du kör ett webbseminarium/en lektion där du vill att alla N-antal deltagare ska ta en provkörning, rekommenderas att du publicerar med N antal heta instanser och sedan när klassen är över för att publicera tillbaka till ditt normala antal Heta instanser.

- **Provkörningslängd (timmar) -** *[Obligatoriskt fält]* Varaktighet för hur \# länge provkörningen ska vara aktiv, i timmar. Provkörningen avslutas automatiskt när den här tidsperioden är.

- **Namn på Azure Resource Group -** *[Obligatoriskt fält]* Skriv i resursgruppsnamnet där logikapptestenheterna sparas.

- **Tilldela logikappnamn -** *[Obligatoriskt fält]* Skriv i logikappen som används för att tilldela en användare i testenheten innan kunden får det, skriv i namnet på logic-appen här. Kontrollera att filen sparas i resursgruppen ovan.

- **Avetablering Logic App Name -** *[Obligatoriskt fält]* Skriv i Logic App namn för din avetablering av alla resurser som skapats i provkörningen. Kontrollera att filen sparas i resursgruppen ovan.

- **Åtkomstinformation -** *[Obligatoriskt fält]* När en kund får sin provkörning visas åtkomstinformationen för dem. De här instruktionerna är avsedda att dela de användbara utdataparametrarna från mallen Test Drive Resource Manager. Om du vill inkludera utdataparametrar använder du dubbla kladdiga parenteser (till exempel **{{outputname}}**) och de infogas korrekt på platsen. (HTML-strängformatering rekommenderas här för att återge i fronten).

### <a name="test-drive-deployment-subscription-details"></a>Prenumerationsinformation för distribution av testkörning

Det sista avsnittet som ska fyllas i är att kunna distribuera testenheterna automatiskt genom att ansluta din Azure-prenumeration och Azure Active Directory (AD).

![Prenumerationsinformation för Test Drive-distribution](./media/azure-resource-manager-test-drive/subdetails1.png)

**Azure-prenumerations-ID** *[Obligatoriskt fält]* Detta ger åtkomst till Azure-tjänster och Azure-portalen. Prenumerationen är där resursanvändning rapporteras och tjänster faktureras. Om du inte redan har en **separat** Azure-prenumeration för testenheter, gå vidare och gör en. Du hittar Azure-prenumerations-ID:er genom att logga in på Azure-portalen och navigera till prenumerationerna på menyn till vänster.
(Exempel: "a83645ac-1234-5ab6-6789-1h234g764ghty")

![Azure-prenumerationer](./media/azure-resource-manager-test-drive/subdetails2.png)

**Azure AD Tenant ID** *[Obligatoriskt fält]* Om du redan har ett klient-ID som redan finns tillgängligt hittar du det nedan i Properties -\> Directory ID.

![Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails3.png)

Annars skapar du en ny klient i Azure Active Directory.

![Egenskapsskärmen för Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails4.png)

! Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails5.png)

![Azure Active Directory-klienter](./media/azure-resource-manager-test-drive/subdetails6.png)

**Azure AD App ID** *[Obligatoriskt fält]* Nästa steg är att skapa och registrera ett nytt program. Vi använder det här programmet för att utföra åtgärder på din Test Drive-instans.

1. Navigera till den nyligen skapade katalogen eller redan befintlig katalog och välj Azure Active Directory i filterfönstret.
2. Sök "App registreringar" och klicka på "Lägg till"
3. Ange ett programnamn.
4. Välj typ av som "Webbapp / API"
5. Ange något värde i Sign-on\'URL, kommer vi inte att använda det fältet.
6. Klicka på Skapa.
7. När programmet har skapats går\> du till Egenskaper - Ange programmet som flera innehavare och träffar Spara.

Klicka på Spara. Det sista steget är att ta tag i program-ID för den här registrerade appen och klistra in det i fältet Test Drive här.

![ID för Azure Active Directory-programmet](./media/azure-resource-manager-test-drive/subdetails7.png)

Med tanke på att vi använder programmet för att distribuera till prenumerationen måste vi lägga till programmet som en deltagare i prenumerationen. Instruktionerna för dessa är följande:

1. Navigera till bladet Prenumerationer och välj lämplig prenumeration som du endast använder för provkörningen.
1. Klicka på **Åtkomstkontroll (IAM)**.
1. Klicka på fliken **Rolltilldelningar.**  ![Azure Active Directory, lägga till ett nytt huvudnamn för åtkomstkontroll](./media/azure-resource-manager-test-drive/SetupSub7_1.jpg)
1. Klicka på **Lägg till rolltilldelning**.
1. Ange rollen som **deltagare**.
1. Skriv in namnet på Azure AD-programmet och välj programmet för att tilldela rollen.
    ![Azure Active Directory-behörigheter](./media/azure-resource-manager-test-drive/SetupSub7_2.jpg)
1. Klicka på **Spara**.

**Azure AD App Key -** *[Obligatoriskt fält]* Det sista fältet är att generera en autentiseringsnyckel. Lägg till en nyckelbeskrivning under tangenter, ange att varaktigheten aldrig ska upphöra att gälla och välj sedan spara. Det är **viktigt** att undvika att ha en utgången nyckel, som kommer att bryta din provkörning i produktion. Kopiera det här värdet och klistra in det i fältet Test Drive.

![Avsnittet Azure Active Directory-nycklar](./media/azure-resource-manager-test-drive/subdetails8.png)

> [!CAUTION]
> Du kan inte använda förhandsversionen av Azure App Registration eftersom den för närvarande inte genererar en base64-kodad nyckel.


<a name="next-steps"></a>Nästa steg
----------

Nu när du har fyllt i alla testkörningsfält går du igenom och **publicerar om** erbjudandet. När din provkörning har klarat certifieringsprocessen bör du testa kundupplevelsen i **förhandsversionen** av erbjudandet. Starta en provkörning i användargränssnittet och kontrollera att testenheterna distribueras korrekt.

Det är viktigt att notera att du inte tar bort någon del av testenheten eftersom de är etablerade för dina kunder, så testkörningstjänsten rensar automatiskt dessa resursgrupper när en kund är klar med den.

När du känner dig bekväm med ditt förhandserbjudande, nu är det dags att **gå live!** Det finns en slutlig granskningsprocess från Microsoft när erbjudandet har publicerats för att dubbelkolla hela slutet till slut-upplevelsen. Om erbjudandet av någon anledning avvisas skickar vi ett meddelande till den tekniska kontakten för ditt erbjudande som förklarar vad som behöver fixas.

Om du har fler frågor, letar efter felsökningsråd eller vill göra din testkörning mer framgångsrik, gå till [vanliga frågor, felsökning, & bästa praxis](./marketing-and-best-practices.md).
