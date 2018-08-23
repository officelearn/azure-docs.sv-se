---
title: Rapportering om Azure Active Directory Automatisk etablering av användarkonto till SaaS-program | Microsoft Docs
description: Lär dig hur du kontrollerar status för automatisk användarkonto Etableringsjobb och hur du felsöker etableringen av enskilda användare.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: barbkess
ms.reviewer: asmalser
ms.openlocfilehash: 3231792ed1cb7f80cd2a106faf3760c48dd13c19
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2018
ms.locfileid: "42059690"
---
# <a name="tutorial-reporting-on-automatic-user-account-provisioning"></a>Självstudie: Rapportering om automatisk användarens kontoetablering


Azure Active Directory innehåller en [användarkonto etableringstjänsten](active-directory-saas-app-provisioning.md) som hjälper till att automatisera den etablering inaktivering av användarkonton i SaaS-appar och andra system för slutpunkt till slutpunkt identitetslivscykel hantering. Azure AD stöder förintegrerade användaretablering anslutningar för alla program och system i avsnittet ”aktuella” för den [Azure AD-programgalleriet](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1&subcategories=featured).

Den här artikeln beskrivs hur du kontrollerar etableringsstatusen jobb när de har ställts in och hur du felsöker etablering av enskilda användare och grupper.

## <a name="overview"></a>Översikt

Etablering kopplingar är att ställa in och konfigureras med den [Azure-portalen](https://portal.azure.com), genom att följa den [tillhandahålls dokumentation](saas-apps/tutorial-list.md) för programmet som stöds. När konfigurerad och körs, etablering jobb kan du rapportera om hur du använder en av två metoder:

* **Azure-hanteringsportalen** -den här artikeln beskriver främst hämtar rapportinformation från den [Azure-portalen](https://portal.azure.com), som innehåller både en etablering sammanfattningsrapport samt detaljerade etablering granskningsloggar för en visst program.

* **Granska API** -Azure Active Directory innehåller också en granska API som ger programmatisk hämtning av detaljerade etablering granskningsloggarna. Se [Azure Active Directory granska API-referens](active-directory-reporting-api-audit-reference.md) för hur du använder den här API-dokumentation. Även om den här artikeln inte beskriver hur du använder API: et specifikt, den redogör för typerna av etablering händelser som registreras i granskningsloggen.

### <a name="definitions"></a>Definitioner

Den här artikeln använder följande termer som anges nedan:

* **Käll-System** -lagringsplatsen för användare som den Azure AD-etableringtjänsten synkroniserar från. Azure Active Directory är källsystemet för flesta förintegrerade etablering kopplingar, men det finns vissa undantag (exempel: Workday-inkommande synkronisering).

* **Rikta System** -lagringsplatsen för användare som den Azure AD-etableringtjänsten synkroniserar till. Detta är vanligtvis ett SaaS-program (exempel: Salesforce, ServiceNow, Google Apps, Dropbox for Business), men i vissa fall kan vara ett lokalt system, till exempel Active Directory (exempel: Workday inkommande synkronisering till Active Directory).


## <a name="getting-provisioning-reports-from-the-azure-management-portal"></a>Hämta etablering rapporter från Azure-hanteringsportalen

Om du vill hämta etablering rapportinformation för ett visst program, starta genom att starta den [Azure-hanteringsportalen](https://portal.azure.com) och bläddra till företagsprogram som etablering har konfigurerats. Du etablerar användare att LinkedIn upphöja är navigeringssökvägen till programinformation:

**Azure Active Directory > företagsprogram > alla program > LinkedIn höjer**

Härifrån kan du komma åt både en sammanfattningsrapport för etablering och allokering granskningsloggar, både som beskrivs nedan.


## <a name="provisioning-summary-report"></a>Etablering sammanfattningsrapport

Etablering sammanfattningsrapporten syns i den **etablering** fliken för angivna program. Finns den i den **synkroniseringsinformation** avsnittet under **inställningar**, och innehåller följande information:

* Det totala antalet användare och / grupper som har synkroniserats och finns för närvarande i omfånget för etablering mellan källsystemet och målsystemet

* Tid för senaste synkronisering kördes. Synkronisering sker vanligtvis var 20 – 40 minuter efter en [inledande synkronisering](active-directory-saas-app-provisioning.md#what-happens-during-provisioning) har slutförts.

* Huruvida en [inledande synkronisering](active-directory-saas-app-provisioning.md#what-happens-during-provisioning) har slutförts

* Huruvida etableringen har placerats i karantän och nyheter statusorsaken för karantän (t.ex, det gick inte att kommunicera med målsystemet på grund av ogiltiga autentiseringsuppgifter)

Etablering sammanfattningsrapporten ska vara den första plats administratörer titt att läsa på driftstatusen för etableringsjobbet för.

 ![Sammanfattningsrapport](./media/active-directory-saas-provisioning-reporting/summary_report.PNG)

## <a name="provisioning-audit-logs"></a>Etablering granskningsloggar
Alla aktiviteter som utförs av etableringstjänsten registreras i Azure AD-granskningsloggar, som kan visas i den **granskningsloggar** fliken den **Kontoetablering** kategori. Händelsetyper loggade aktiviteter är:

* **Importera händelser** -registreras en ”import”-händelse varje gång den Azure AD-etableringtjänsten hämtar information om en enskild användare eller grupp från en källsystemet eller målsystemet. Under synkroniseringen hämtas användare från källsystemet först med resultat som är registrerade som ”importera” händelser. De matchande ID: N hämtades användare tillfrågas sedan mot målsystemet och kontrollera om de finns med resultat som också registreras som ”importera” händelser. De här händelserna registrera alla mappade användarattribut och deras värden som har setts av de Azure AD-etableringtjänsten vid tidpunkten för händelsen. 

* **Synkronisering regeln händelser** – dessa händelser rapport om resultatet av attributmappning regler och eventuella konfigurerad Omfångsfilter, när användarens data har importerats och utvärderas från käll-och mål. Till exempel om en användare i en källsystemet anses vara i omfånget för etablering och anses inte finns i målsystemet, och sedan på den här händelsen posterna som kommer användaren att tillhandahållas i målsystemet. 

* **Exportera händelser** -registreras en ”export”-händelse varje gång den Azure AD-etableringtjänsten skriver ett användarobjekt för konto eller grupp till ett målsystem. De här händelserna registrera alla användarattribut och deras värden som har skrivits av Azure AD etableringstjänsten vid tidpunkten för händelsen. Om ett fel uppstod vid skrivning till användarobjektet konto eller grupp till målsystemet, visas det här.

* **Bearbeta händelser escrow** -processen escrows inträffa när etableringstjänsten påträffar ett fel vid försök till operationer och börjar att göra om åtgärden på en backoffintervall tid. Varje gång som en åtgärd för etablering drogs tillbaka registreras en ”escrow”-händelse.

När du visar etablering händelser för en enskild användare, sker normalt i den här ordningen:

1. Importera händelse: användaren har hämtats från källsystemet.

2. Importera händelse: målsystemet efterfrågas för att kontrollera om finns för den hämtade användaren.

3. Synkronisering regeln händelse: användardata från käll- och system som ska utvärderas mot de konfigurerade attributmappning regler och Omfångsfilter att fastställa vilken åtgärd, om sådana finns, som ska utföras.

4. Exportera händelse: om händelsen synkronisering regel anges att en åtgärd ska utföras (Lägg till, uppdatera, ta bort), och sedan resultatet av åtgärden sparas i en Export-händelse.

![Skapa en Azure AD-användare för testning](./media/active-directory-saas-provisioning-reporting/audit_logs.PNG)


### <a name="looking-up-provisioning-events-for-a-specific-user"></a>Leta upp etablering händelser för en viss användare

Den vanligaste för etablering granskningsloggarna är att kontrollera Etableringsstatus för ett enskilt användarkonto. Att leta upp de senaste etablering händelserna för en viss användare:

1. Gå till den **granskningsloggar** avsnittet.

2. Från den **kategori** menyn och välj **Kontoetablering**.

3. I den **datumintervall** menyn, Välj datumintervall som du vill söka i,

4. I den **Search** stapeln, ange användar-ID för den användare som du vill söka efter. Formatet för ID-värdet måste matcha det du valt som primärt matchande ID i attributmappning konfigurationen (till exempel userPrincipalName eller medarbetare ID-nummer). ID-värdet som krävs kommer att visas i kolumnen mål.

5. Tryck på RETUR för att söka. Senaste etablering händelser kommer att returneras först.

6. Observera aktivitetstyper och om de har lyckats eller misslyckats om händelser som returneras. Om inga resultat returneras, betyder det att användaren antingen finns inte eller har inte har upptäckts av etableringsprocessen om en fullständig synkronisering inte har slutförts ännu.

7. Klicka på enskilda händelser för att visa mer information, inklusive alla egenskaper för användare som har hämtats, utvärderas eller skrivs som en del av händelsen.

En demonstration om hur du använder granskningsloggarna finns i videon nedan. Granskningsloggarna visas runt 5:30 Markera:

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

### <a name="tips-for-viewing-the-provisioning-audit-logs"></a>Tips för att visa granskningsloggarna etablering

För bästa läsbarhet i Azure-portalen väljer du den **kolumner** knappen och väljer dessa kolumner:

* **Datum** -visar det datum då händelsen inträffade.
* **Mål** – visar den app och användar-ID som omfattas av händelsen.
* **Aktiviteten** -aktivitetstyp som tidigare beskrivits.
* **Status för** – oavsett om händelsen lyckades eller inte.
* **Statusorsak** – en sammanfattning av vad som hände i händelsen etablering.


## <a name="troubleshooting"></a>Felsökning

Etablering sammanfattande rapport och granska loggarna spela en viktig roll som hjälper administratörer felsöka olika användarkonto konfigurationsproblem.

Scenariobaserade vägledning om hur du felsöker automatisk användaretablering finns i [problem med att konfigurera och etablera användare till ett program](active-directory-application-provisioning-content-map.md).


## <a name="additional-resources"></a>Ytterligare resurser

* [Hantering av användarkontoetablering för Företagsappar](manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)
