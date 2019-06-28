---
title: Ta reda på när en viss användare kommer att kunna komma åt ett program | Microsoft Docs
description: Hur du tar reda på när en ytterst viktiga användare kunna komma åt ett program som du har konfigurerat för etableringen av användare med Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1fd6b70e7a4542a4cad2ee95fa280ddf8fbe6553
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2019
ms.locfileid: "67310040"
---
# <a name="check-the-status-of-user-provisioning"></a>Kontrollera status för etableringen av användare

Den Azure AD-etableringtjänsten körs en inledande etablering cykel mot källsystemet och målsystemet, följt av periodiska inkrementella cykler. När du konfigurerar etablering för en app kan du kontrollera den aktuella statusen för etableringstjänsten och se när en användare kommer att kunna komma åt en app.

## <a name="view-the-provisioning-progress-bar-preview"></a>Visa etableringen förloppsfältet (förhandsversion)

 På den **etablering** sida för en app kan du visa status för den Azure AD-etableringstjänsten. Den **aktuell Status** avsnittet längst ned på sidan visas om en allokering cykel har börjat etableras användarkonton. Du kan se förloppet för cykel, se hur många användare och grupper har etablerats och se hur många roller som skapas.

När du först konfigurera automatisk etablering i **aktuell Status** avsnittet längst ned på sidan visas status för den första etablering cykeln. Det här avsnittet uppdateras varje gång en inkrementell cykeln körs. Följande information visas:
- Typ av etablering cykel (inledande eller inkrementell) som körs för tillfället eller senast kördes.
- En **förloppsindikatorn** som visar procentandelen av den etablering cykel som har slutförts. Procentandelen visar antalet sidor som har etablerats. Observera att varje sida kan innehålla flera användare eller grupper, så att procentandelen inte direkt stämmer med antalet användare, grupper eller roller som har etablerats.
- En **uppdatera** knappen som du kan använda för att hålla vyn uppdateras.
- Antalet **användare** och **grupper** etablerats och antal roller som skapas. Under den inledande cykeln den **användare** tal räknar med 1 om en användare skapas eller uppdateras och den räknar med 1 när en användare tas bort. Under en inkrementell cykel, uppdateras inte påverkar den **användare** Antal; antalet ändringarna endast när användare skapas eller tas bort.
- En **visa granskningsloggarna** länkar, vilket öppnar Azure AD-granskningen loggar för information om alla åtgärder som körs av användaren etableringstjänsten, inklusive Etableringsstatus för enskilda användare (se den [Använd granskningsloggar](#use-audit-logs-to-check-a-users-provisioning-status)nedan).

När en allokering cykel har slutförts, den **statistik hittills** visar ackumulerade antalet användare och grupper som har etablerats till datum, tillsammans med datum för slutförd och varaktighet för senaste cykel. Den **aktivitets-ID** unikt identifierar den senaste etablering cykeln. Den **jobb-ID** är en unik identifierare för etablering jobbet och är specifik för appen i din klient.

Etablering pågår kan ses i Azure-portalen i den **Azure Active Directory &gt; Företagsappar &gt; \[programnamn\] &gt; etablering** fliken.

![Etablering sidan förloppsindikator](media/application-provisioning-when-will-provisioning-finish-specific-user/provisioning-progress-bar-section.png)

## <a name="use-audit-logs-to-check-a-users-provisioning-status"></a>Använd granskningsloggar för att kontrollera en användares Etableringsstatus

Om du vill se Etableringsstatus för en vald användare läser du granskningsloggarna i Azure AD. Alla åtgärder som körs av användaren provisioning-tjänst registreras i Azure AD granskningsloggar. Detta inkluderar alla Läs- och skrivåtgärder som gjorts i käll-och mål- och användardata som har lästs eller skrivits under varje åtgärd.

Etablering granskningsloggarna är tillgängliga i Azure-portalen i den **Azure Active Directory &gt; Företagsappar &gt; \[programnamn\] &gt; granskningsloggarna** fliken. Filtrera loggarna på den **Kontoetablering** kategori för att bara se händelserna etablering för appen. Du kan söka efter användare baserat på ”matchande ID” som har konfigurerats för dem i attributmappningarna. 

Exempel: Om du har konfigurerat ”user principal name” eller ”e-postadress” som det matchande attributet på Azure AD-sida och användaren inte etablera har värdet ”audrey@contoso.com”, sedan Sök i granskningsloggarna för ”audrey@contoso.com” och gå sedan igenom poster returnerade.

Etablering granskningsloggarna registrera alla åtgärder som utförs av etableringstjänsten, inklusive:

* Ställa frågor till Azure AD för tilldelade användare som är inom omfånget för etablering
* Fråga målappen förekomsten av dessa användare
* Jämföra användarobjekt mellan systemet
* Att lägga till, uppdatera eller inaktivera användarkontot i målsystemet baserat på en jämförelse

Mer information om hur du tolkar granskningsloggarna i Azure-portalen finns i den [etablering rapporteringsguide](check-status-user-account-provisioning.md).

## <a name="how-long-will-it-take-to-provision-users"></a>Hur lång tid tar det för att etablera användare?
När du använder automatisk användaretablering med ett program, Azure AD automatiskt etablerar och uppdaterar användarkonton i en app baserat på saker som [användar- och grupptilldelningar](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) vid en schemalagd tid intervall, vanligtvis var tionde minut.

Den tid det tar för en viss användare som ska etableras beror huvudsakligen på om din Etableringsjobbet körs en inledande synkronisering eller en inkrementell synkronisering.

- För **inledande synkroniseringar**, jobbtiden beror på många faktorer, bland annat hur många användare och grupper i omfånget för etablering, och det totala antalet användare och grupper i källsystemet. Den första synkroniseringen mellan Azure AD och en app kan ta allt från 20 minuter till flera timmar beroende på storleken på Azure AD-katalog och antalet användare inom omfånget för etablering. En omfattande lista över faktorer som påverkar prestanda för den inledande synkroniseringen sammanfattas senare i det här avsnittet.

- För **inkrementella synkroniseringar** efter den första synkroniseringen jobbtiden tenderar att vara snabbare (t.ex. inom 10 minuter), som lagrar vattenstämplar som representerar tillståndet för båda systemen efter den inledande synkroniseringen, förbättra prestanda för etableringstjänsten för efterföljande synkroniseringar. Jobbtiden beror på antalet ändringar som har identifierats i den etablering cykeln. Om det finns färre än 5 000 användare eller ändringar i gruppmedlemskap, kan jobbet slutförs inom en enda inkrementella etablering cykel. 

I följande tabell sammanfattas synkroniseringstider för vanliga scenarier för etablering. I dessa scenarier källsystemet är Azure AD och målsystemet är ett SaaS-program. Synkroniseringstiderna härleds från en statistiska analyser av Synkroniseringsjobb för SaaS-program, ServiceNow, arbetsplats, Salesforce och G Suite.


| Omfattningskonfigurationen | Användare, grupper och medlemmar i omfånget | Den inledande synkroniseringstiden | Inkrementell synkronisering |
| -------- | -------- | -------- | -------- |
| Synkronisera tilldelade användare och grupper bara |  < 1,000 |  < 30 minuter | < 30 minuter |
| Synkronisera tilldelade användare och grupper bara |  1 000 – 10 000 | 142 - 708 minuter | < 30 minuter |
| Synkronisera tilldelade användare och grupper bara |   10,000 - 100,000 | 1,170 - 2,340 minuter | < 30 minuter |
| Synkronisera alla användare och grupper i Azure AD |  < 1,000 | < 30 minuter  | < 30 minuter |
| Synkronisera alla användare och grupper i Azure AD |  1 000 – 10 000 | < 30-120 minuter | < 30 minuter |
| Synkronisera alla användare och grupper i Azure AD |  10,000 - 100,000  | 713 - 1,425 minuter | < 30 minuter |
| Synkronisera alla användare i Azure AD|  < 1,000  | < 30 minuter | < 30 minuter |
| Synkronisera alla användare i Azure AD | 1 000 – 10 000  | 43 - 86 minuter | < 30 minuter |


För konfigurationen **synkronisering tilldelade användare och grupper bara**, du kan använda följande formler för att fastställa ungefärlig minimum och maximum som förväntat **inledande synkronisering** gånger:

    Minimum minutes =  0.01 x [Number of assigned users, groups, and group members]
    Maximum minutes = 0.08 x [Number of assigned users, groups, and group members] 
    
Sammanfattning av faktorer som påverkar den tid det tar för att slutföra en **inledande synkronisering**:

- Det totala antalet användare och grupper i omfånget för etablering.

- Det totala antalet användare, grupper och medlemmar i gruppen finns i källsystemet (Azure AD).

- Om användare inom omfånget för etablering matchas mot befintliga användare i målprogrammet eller måste skapas för första gången. Synkroniseringsjobb som alla användare skapas för första gången tar ungefär *dubbelt så länge* som Synkronisera jobb som alla användare matchas mot befintliga användare.

- Antal fel i den [granskningsloggar](check-status-user-account-provisioning.md). Prestanda är långsammare om det finns många fel och etableringstjänsten är i ett tillstånd i karantän.    

- Begära hastighetsbegränsningar och begränsning implementeras av målsystemet. Vissa målsystem implementerar begäran hastighetsbegränsningar och begränsningar, vilket kan påverka prestanda under stora synkroniseringsåtgärder. Under dessa förhållanden kan en app som tar emot för många begäranden för snabbt långsam dess konverteringsfrekvensen eller stänga anslutningen. Anslutningen måste justera genom att inte skicka appförfrågningar snabbare än appen kan bearbeta dem för att förbättra prestanda. Etablering anslutningsverktyg som bygger Microsoft göra den här justering. 

- Antalet och storleken på tilldelade grupper. Det tar längre tid än synkronisera användare att synkronisera tilldelade grupper. Prestanda kan påverkas av både antalet och storleken på de tilldelade grupperna. Om ett program har [mappningar som aktiverats för gruppen objektsynkronisering](customize-application-attributes.md#editing-group-attribute-mappings), egenskaper som gruppnamn och medlemskap har synkroniserats och användare. Dessa ytterligare synkroniseringar tar längre tid än att bara synkronisera användarobjekt.

## <a name="next-steps"></a>Nästa steg
[Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning)
