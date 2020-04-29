---
title: Test enhet för Logic app | Azure Marketplace
description: Förklarar hur du skapar en testen het som ansluter med en Dynamics AX/Azure-instans eller någon annan resurs utöver bara Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 766f893d71ca0830fe8b69c50145603c6544cc3f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80278390"
---
<a name="logic-app-test-drive"></a>Test enhet för Logic app
====================

Den här artikeln är för utgivare som har sitt erbjudande på AppSource och vill bygga en testen het som ansluter med en Dynamics AX/Azure-instans eller någon annan resurs utöver bara Azure.

<a name="how-to-build-a-logic-app-test-drive"></a>Så här skapar du en test enhet för en Logic app
-----------------------------------

Test Drive-dokumentationen för logi Kap par test enheter är för närvarande fortfarande på GitHub för [åtgärder](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) och [kund engagemang](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app), gå till där för att läsa mer.

<a name="how-to-publish-a-test-drive"></a>Publicera en testen het
---------------------------

Nu när du har skapat din test enhet går det här avsnittet igenom vart och ett av de fält som krävs för att du ska kunna publicera test enheten.

![Aktivera funktionen test enhet](./media/azure-resource-manager-test-drive/howtopub1.png)

Det första och viktigaste fältet är att växla över om du vill testa formuläret med alla obligatoriska fält som visas så att du kan fylla i. När du väljer **Nej** inaktive ras formuläret och om du återpublicerar med test enheten inaktive rad tas test enheten bort från produktionen.

*Obs!* om det finns test enheter som används aktivt av användarna fortsätter dessa test enheter att köras tills sessionen upphör att gälla.

### <a name="details"></a>Information

Nästa avsnitt för att fylla i är information om ditt test enhets erbjudande.

![Test driv Rutins information](./media/azure-resource-manager-test-drive/howtopub2.png)

**Beskrivning-** *[obligatoriskt fält]* det är här du skriver den huvudsakliga beskrivningen av vad som finns på test enheten. Kunden kommer att komma hit för att läsa vilka scenarier som test enheten kommer att täcka om din produkt. 

**Användar handbok –** *[obligatoriskt fält]* det här är en djupgående genom gång av test enhets upplevelsen. Kunden öppnar den här och kan gå igenom exakt vad du vill att de ska göra i hela test enheten. Det är viktigt att det här innehållet är enkelt att förstå och följa! (Måste vara en. pdf-fil)

**Prov Drive demo video –** \[rekommenderas\] på liknande sätt som Användar handbok, det är bäst att ta med en video kurs om din test enhets upplevelse. Kunden kommer att titta på det här innan eller under deras testenhet och kan gå igenom exakt vad du vill att de ska göra i hela test enheten. Det är viktigt att det här innehållet är enkelt att förstå och följa!

- **Namn** – rubrik för din video
- **Link** -måste vara en inbäddad URL från YouTube eller Vimeo. Exempel på hur du hämtar den inbäddade URL: en finns nedan:
- **Thumbnail** – måste vara en bild med hög kvalitet (533x324). Vi rekommenderar att du tar en skärm bild av en del av test enhets upplevelsen här.

Nedan visas hur de här fälten visas för kunden under deras test enhets upplevelse.

![Testa enhets fält utseende och känsla](./media/azure-resource-manager-test-drive/howtopub4.png)

### <a name="technical-configuration"></a>Teknisk konfiguration

I nästa avsnitt som du kan fylla i är det där du konfigurerar appen för testenhets logik och definiera hur speciellt dina test enhets instanser fungerar.

![Teknisk konfiguration för test enhet](./media/azure-resource-manager-test-drive/howtopub5_logicapp.png)

- **Region** - *[obligatoriskt fält]* den region som du väljer är där du väljer var du vill att din test enhets Logic app-resurser distribueras i.

    *Obs:* Om din Logi Kap par innehåller anpassade resurser som lagras i en region kontrollerar du att regionen är vald här. Det bästa sättet att göra detta är att **fullständigt distribuera din Logic app lokalt på din Azure-prenumeration i portalen och kontrol lera att den fungerar** innan du skriver den här.

- **Maximalt antal samtidiga test enheter** - *[obligatoriskt fält]* antal test enhets instanser som redan har distribuerats och väntar på åtkomst per vald region. Kunder kan komma åt dessa test enheter direkt i stället för att behöva vänta på en distribution.

    *Obs:* Om du kör en webb seminarium/klass där du vill att alla dina många studenter ska göra en test enhet, rekommenderar vi att du publicerar med N antal aktiva instanser och sedan när klassen är över för att publicera tillbaka till det normala antalet aktiva instanser.

- **Test enhetens varaktighet (timmar)-** *[obligatoriskt fält]* varaktighet för hur länge test enheten förblir aktiv, i \# timmar. Test enheten avslutas automatiskt när den här tids perioden är slut.

- **Azure-resurs grupp namn –** *[obligatoriskt fält]* Skriv i resurs gruppens namn där dina Logic app-Testenheter sparas.

- **Tilldela namn på Logic app-** *[obligatoriskt fält]* Skriv i den Logic-app som används för att tilldela en användare på test enheten innan kunden får den, skriver du in namnet på den Logic-appen här. Se till att filen sparas i resurs gruppen ovan.

- **Avetablera Logic app-namn –** *[obligatoriskt fält]* Skriv i Logic App-namnet för avetablering av alla resurser som skapas på test enheten. Se till att filen sparas i resurs gruppen ovan.

- **Åtkomst information –** *[obligatoriskt fält]* när en kund har fått sin test-enhet visas åtkomst informationen för dem. De här anvisningarna är avsedda att dela användbara utdataparametrar från din Test Drive Resource Manager-mall. Om du vill inkludera utdataparametrar använder du dubbla klammerparenteser (till exempel **{{outputname}}**) och de infogas korrekt på platsen. (HTML-teckenformatering rekommenderas här för rendering på klient sidan).

### <a name="test-drive-deployment-subscription-details"></a>Information om distributions prenumeration för test enhet

Det sista avsnittet att fylla i är att kunna distribuera test enheterna automatiskt genom att ansluta din Azure-prenumeration och Azure Active Directory (AD).

![Information om distributions prenumeration för test enhet](./media/azure-resource-manager-test-drive/subdetails1.png)

**Azure-prenumerations-ID** *[obligatoriskt fält]* detta ger åtkomst till Azure-tjänster och Azure Portal. Prenumerationen är den plats där resursanvändningen rapporteras och tjänsterna faktureras. Om du inte redan har en **separat** Azure-prenumeration för test enheter kan du gå vidare och göra en. Du hittar prenumerations-ID: n för Azure genom att logga in på Azure Portal och navigera till prenumerationerna på den vänstra menyn.
(Exempel: "a83645ac-1234-5AB6-6789-1h234g764ghty")

![Azure-prenumerationer](./media/azure-resource-manager-test-drive/subdetails2.png)

**Azure AD-klient-ID** *[obligatoriskt fält]* om du har ett klient-ID som redan är tillgängligt kan du hitta det\> nedan i egenskaperna-Directory-ID.

![Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails3.png)

Annars skapar du en ny klient i Azure Active Directory.

![Skärmen Azure Active Directory egenskaper](./media/azure-resource-manager-test-drive/subdetails4.png)

! Azure Active Directory] (./Media/Azure-Resource-Manager-Test-Drive/subdetails5.png)

![Azure Active Directory klienter](./media/azure-resource-manager-test-drive/subdetails6.png)

**Azure AD App-ID** *[obligatoriskt fält]* nästa steg är att skapa och registrera ett nytt program. Vi kommer att använda det här programmet för att utföra åtgärder på din test enhets instans.

1. Navigera till den nyligen skapade katalogen eller befintlig katalog och välj Azure Active Directory i filter fönstret.
2. Sök efter "Appregistreringar" och klicka på "Lägg till"
3. Ange ett program namn.
4. Välj typ av "webbapp/API"
5. Ange ett värde för inloggnings-URL. vi vann\'t använder det fältet.
6. Klicka på Skapa.
7. När programmet har skapats går du till egenskaper –\> ange programmet som flera innehavare och tryck på Spara.

Klicka på Spara. Det sista steget är att ta tag i program-ID: t för den här registrerade appen och klistra in den i fältet test enhet här.

![ID för Azure Active Directory-programmet](./media/azure-resource-manager-test-drive/subdetails7.png)

Med tanke på att vi använder programmet för att distribuera till prenumerationen måste vi lägga till programmet som deltagare i prenumerationen. Anvisningarna för dessa är följande:

1. Gå till bladet prenumerationer och välj lämplig prenumeration som du använder endast för test enheten.
1. Klicka på **Åtkomstkontroll (IAM)**.
1. Klicka på fliken **roll tilldelningar** .  ![Azure Active Directory, lägga till ett nytt Access Control-huvudobjekt](./media/azure-resource-manager-test-drive/SetupSub7_1.jpg)
1. Klicka på **Lägg till roll tilldelning**.
1. Ange rollen som **deltagare**.
1. Skriv namnet på Azure AD-programmet och välj det program som rollen ska tilldelas till.
    ![Azure Active Directory behörigheter](./media/azure-resource-manager-test-drive/SetupSub7_2.jpg)
1. Klicka på **Spara**.

**Azure AD App nyckel-** *[obligatoriskt fält]* det sista fältet är att generera en autentiseringsnyckel. Under nycklar lägger du till en nyckel beskrivning, ställer in varaktigheten så att den aldrig upphör att gälla och väljer sedan Spara. Det är **viktigt** att undvika att ha en utgånget nyckel, vilket innebär att din test enhet bryts i produktionen. Kopiera det här värdet och klistra in det i fältet för den obligatoriska test enheten.

![Avsnittet Azure Active Directory nycklar](./media/azure-resource-manager-test-drive/subdetails8.png)

> [!CAUTION]
> Du kan inte använda förhands granskningen Azure Apps registrering eftersom den för närvarande inte genererar en Base64-kodad nyckel.


<a name="next-steps"></a>Nästa steg
----------

Nu när du har fyllt i alla dina test enhets fält går du igenom och **publicerar** om erbjudandet. När test enheten har klarat certifierings processen bör du testa kund upplevelsen i för **hands versionen** av ditt erbjudande. Starta en testen het i användar gränssnittet och kontrol lera att test enheterna är fullständigt distribuerade.

Det är viktigt att Observera att du inte tar bort någon del av test enheten eftersom de är etablerade för dina kunder, så att test enhets tjänsten rensar automatiskt dessa resurs grupper efter att en kund har slutförts.

När du känner dig bekvämt med ditt för hands erbjudande är det dags att **gå live**! Det finns en slutgiltig gransknings process från Microsoft när erbjudandet har publicerats för att dubbelt kontrol lera hela slut punkt till slut punkt. Om erbjudandet avvisas av någon anledning kommer vi att skicka ett meddelande till teknisk kontakt för ditt erbjudande som förklarar vad som behöver åtgärdas.

Om du har fler frågor, letar efter fel söknings råd eller vill göra en test enhet mer framgångs rik kan du gå till [vanliga frågor och svar, felsöka & bästa praxis](./marketing-and-best-practices.md).
