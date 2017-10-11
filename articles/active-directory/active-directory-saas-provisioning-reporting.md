---
title: "Rapportering av Azure Active Directory automatisk konto användaretablering för SaaS-program | Microsoft Docs"
description: "Lär dig hur du kontrollerar status för automatisk användarkonto Etableringsjobb och felsökning av etableringen av enskilda användare."
services: active-directory
documentationcenter: 
author: asmalser-msft
writer: asmalser-msft
manager: stevenpo
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/12/2017
ms.author: asmalser-msft
ms.openlocfilehash: 86b9a3d93745045904c6038583b9bc6ebac5667e
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="tutorial-reporting-on-automatic-user-account-provisioning"></a>Självstudier: Rapportering om etablering av en automatisk användar-konto


Azure Active Directory innehåller en [användarkontot etableras](active-directory-saas-app-provisioning.md) som hjälper till att automatisera etablering Frigör etableringen av användarkonton i SaaS-appar och andra system för slutpunkt till slutpunkt identitet livscykel hantering. Azure AD stöder förintegrerade användaretablering kopplingar för alla program och system i avsnittet ”aktuell” i den [Azure AD application gallery](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/azure-active-directory-apps?page=1&subcategories=featured).

Den här artikeln beskriver hur du kontrollerar statusen för Etableringsjobb när de har ställts in och hur du felsöker etableringen av enskilda användare och grupper.

## <a name="overview"></a>Översikt

Etablering kopplingar är främst ställa in och konfigurerats för att använda den [Azure-hanteringsportalen](https://portal.azure.com), genom att följa den [tillhandahålls dokumentationen](active-directory-saas-tutorial-list.md) för programmet där etablering av kontot är det önskade. När konfigurerad och igång, kan Etableringsjobb för ett program rapporteras på ett av två sätt:

* **Azure-hanteringsportalen** -den här artikeln beskriver främst hämtar rapportinformation från den [Azure-hanteringsportalen](https://portal.azure.com), som innehåller både en allokering sammanfattningsrapport samt detaljerad etablering granskning loggar för ett visst program.

* **Granska API** -Azure Active Directory innehåller också en granska API som ger programmatisk hämtning av detaljerad etablering granskningsloggarna. Se [Azure Active Directory audit API-referens](active-directory-reporting-api-audit-reference.md) för hur du använder den här API-dokumentation. När den här artikeln inte specifikt beskriver hur du använder API: et den information om vilka typer av händelser som registreras i granskningsloggen-etablering.

### <a name="definitions"></a>Definitioner

Den här artikeln använder följande termer som anges nedan:

* **Datakällan System** -databas med användare som Azure AD etableras synkroniseras från. Azure Active Directory är källsystemet för flesta förintegrerade etablering kopplingar, men det finns vissa undantag (exempel: Workday inkommande synkronisering).

* **Rikta System** -databas med användare som Azure AD etableras har synkroniserats. Detta är vanligtvis ett SaaS-program (exempel: Salesforce, ServiceNow, Google Apps, Dropbox för företag), men i vissa fall kan vara ett lokalt system, till exempel Active Directory (exempel: Workday inkommande synkronisering till Active Directory).


## <a name="getting-provisioning-reports-from-the-azure-management-portal"></a>Hämta etablering rapporter från Azure-hanteringsportalen

Om du vill hämta etablering rapportinformation för ett visst program, starta genom att starta den [Azure-hanteringsportalen](https://portal.azure.com) och bläddra till företagsprogram som etablering har konfigurerats. Du etablerar du höjer LinkedIn är navigeringssökvägen till programinformation:

**Azure Active Directory > företagsprogram > alla program > LinkedIn höjer**

Härifrån kan du har åtkomst till både sammanfattningsrapporten etablering och allokering granskningsloggarna, både beskrivs nedan.


### <a name="provisioning-summary-report"></a>Etablering sammanfattningsrapport

Etablering sammanfattningsrapporten visas i den **etablering** fliken för det angivna programmet. Det finns i avsnittet information om synkronisering under **inställningar**, och innehåller följande information:

* Det totala antalet användare och grupper som har synkroniserats och finns för närvarande i omfånget för etablering mellan källsystemet och målsystemet.

* Tid för senaste synkronisering kördes. Synkronisering sker vanligtvis var 20 – 40 minuter efter en fullständig synkronisering har slutförts.

* Huruvida en första fullständig synkronisering har slutförts.

* Huruvida etableringen har placerats i karantän, och vad Statusanledning för karantän som är t.ex. (fel ska kunna kommunicera med målsystemet på grund av ogiltig administratörsautentiseringsuppgifter)

Etablering sammanfattningsrapporten ska hur administratörer första plats för att kontrollera om driftstatusen för etableringsjobbet för.

 ![Översiktsrapport](./media/active-directory-saas-provisioning-reporting/summary_report.PNG)

### <a name="provisioning-audit-logs"></a>Etablering granskningsloggar
Alla aktiviteter som utförs av etablering tjänsten registreras i Azure AD-granskningsloggarna, som kan visas i den **granskningsloggar** fliken den **Kontoetablering** kategori. Loggade händelsen aktivitetstyper inkluderar:

* **Importera händelser** -en ”import” händelse registreras varje gång som Azure AD etableras hämtar information om en enskild användare eller grupp från källsystemet eller målsystemet. Under synkroniseringen hämtas användare från källsystemet först med resultat som är registrerade som ”importera” händelser. Matchande ID hämtade användare tillfrågas sedan mot målsystemet ska kontrollera om de finns med resultat som också är registrerad som ”importera” händelser. Händelserna registreras alla mappade användarattribut och deras värden som visades av Azure AD etableras vid tidpunkten för händelsen. 

* **Synkronisering regeln händelser** - händelserna rapportera resultaten av attributet mappningsregler och eventuella konfigurerade målgrupp filter när informationen har importerats och utvärderas från käll-och mål. Till exempel om en användare i ett källsystem anses vara i omfånget för etablering och anses inte finns i målsystemet och sedan denna händelse registrerar som kommer användaren att tillhandahållas i målsystemet. 

* **Exportera händelser** -en ”export” händelse registreras varje gång som Azure AD etableras skriver ett användarobjekt för kontot eller grupp till ett målsystem. Händelserna registreras alla användarattribut och deras värden som har skrivits av Azure AD etableras vid tidpunkten för händelsen. Om ett fel uppstod vid skrivning till användarobjektet konto eller en grupp till målsystemet, kommer den att visas här.

* **Bearbeta escrow händelser** -processen escrows inträffa när tjänsten etablering påträffar ett fel när en åtgärd och börjar försök igen med ett intervall som inte tid. En ”escrow” händelse registreras varje gång en etableringsåtgärden har dragits tillbaka.

När du tittar på etablering händelser för en enskild användare, inträffar normalt händelserna i den här ordningen:

1. Importera händelse: användaren har hämtats från källsystemet.

2. Importera händelse: målsystemet efterfrågas för att kontrollera om finns för den hämtade användaren.

3. Regeln synkroniseringshändelsen: användardata från käll- och system utvärderas mot attributet konfigurerade matchningsregler och Omfångsfilter för att fastställa vilken åtgärd eventuella ska utföras.

4. Exportera händelse: om händelsen synkronisering regel anges att åtgärden ska utföras (t.ex. Lägg till, uppdatera, ta bort), och sedan resultatet av åtgärden registreras i en Export-händelse.

![Skapa en testanvändare i Azure AD](./media/active-directory-saas-provisioning-reporting/audit_logs.PNG)


### <a name="looking-up-provisioning-events-for-a-specific-user"></a>Leta upp etablering händelser för en viss användare

I de flesta användningsfall för etablering granskningsloggarna är att kontrollera Etableringsstatus för ett användarkonto för enskilda. Att söka efter de senaste etablering händelserna för en specifik användare:

1. Gå till den **granskningsloggar** avsnitt.

2. Från den **kategori** väljer du **Kontoetablering**.

3. I den **datumintervall** -menyn, välj datumintervallet som du vill söka,

4. I den **Sök** menyraden, ange användar-ID för den användare som du vill söka efter. Formatet på ID-värde ska matcha det du valt som primärt matchande ID i attributet mappningskonfigurationen (t.ex. userPrincipalName eller medarbetare ID-nummer). ID-värde som krävs visas i kolumnen Target(s).

5. Tryck på RETUR för att söka. Senaste etablering händelser returneras först.

6. Om händelser returneras Observera aktivitetstyperna och om de lyckades eller misslyckades. Om inga resultat returneras sedan innebär användaren antingen finns inte eller har inte ännu har identifierats av etableringsprocessen om en fullständig synkronisering inte har slutförts ännu.

7. Klicka på enskilda händelser för att visa utökad information, inklusive alla egenskaper för användare som har hämtats, utvärderas eller skrivs som en del av händelsen.


### <a name="tips-for-viewing-the-provisioning-audit-logs"></a>Tips för att visa etablering granskningsloggar

För bästa läsbarhet i Azure portal väljer du den **kolumner** knappen och välj dessa kolumner:

* **Datum** -visar datumet händelsen inträffade.
* **Target(s)** -visas i appen och användar-ID som omfattas av händelsen.
* **Aktiviteten** -aktivitetstyp, enligt beskrivningen ovan.
* **Status för** – oavsett om händelsen lyckades eller inte.
* **Statusanledning** -en sammanfattning av vad som hände i händelsen etablering.


## <a name="troubleshooting"></a>Felsökning

Etablering sammanfattning rapport och granska loggarna spela en viktig roll som hjälper administratörer felsöka olika användarkonto etablering problem.

Scenariobaserade vägledning om hur du felsöker automatisk användaretablering finns [problem med att konfigurera och etablera användare till ett program](active-directory-application-provisioning-content-map.md).


## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användare konto-etablering för företag-appar](active-directory-enterprise-apps-manage-provisioning.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)
