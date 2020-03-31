---
title: Ta reda på när en viss användare kan komma åt en app
description: Ta reda på när en kritiskt viktig användare kan komma åt ett program som du har konfigurerat för användaretablering med Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48370e2806b70d550bce95ceff3857a79561f247
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264146"
---
# <a name="check-the-status-of-user-provisioning"></a>Kontrollera status för användaretablering

Azure AD-etableringstjänsten kör en inledande etableringscykel mot källsystemet och målsystemet, följt av periodiska inkrementella cykler. När du konfigurerar etablering för en app kan du kontrollera etableringstjänstens aktuella status och se när en användare kan komma åt en app.

## <a name="view-the-provisioning-progress-bar"></a>Visa förloppsindikatorn för etablering

 På **etableringssidan** för en app kan du visa status för Azure AD-etableringstjänsten. Avsnittet **Aktuell status** längst ned på sidan visar om en etableringscykel har börjat etablera användarkonton. Du kan titta på förloppet för cykeln, se hur många användare och grupper som har etablerats och se hur många roller som skapas.

När du först konfigurerar automatisk etablering visas status för den första etableringscykeln i avsnittet **Aktuell status** längst ned på sidan. Det här avsnittet uppdateras varje gång en inkrementell cykel körs. Följande uppgifter visas:
- Den typ av etableringscykel (initial eller inkrementell) som körs eller senast slutfördes.
- En **förloppsindikator** som visar procentandelen av etableringscykeln som har slutförts. Procentsatsen återspeglar antalet avstablerade sidor. Observera att varje sida kan innehålla flera användare eller grupper, så att procentsatsen inte direkt korrelerar med antalet användare, grupper eller roller som har etablerats.
- Knappen **Uppdatera** kan du använda för att hålla vyn uppdaterad.
- Antalet **användare** och **grupper** i anslutningsdatalagret. Antalet ökar när ett objekt läggs till i etableringsområdet. Antalet kommer inte att gå ner om en användare är mjuk-bort eller hårt bort eftersom detta inte tar bort objektet från anslutningsdatalagret. Antalet kommer att recakuleras den första synkroniseringen efter cds [återställs](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http) 
- En länk **för visa granskningsloggar,** som öppnar Azure AD-etableringsloggarna för mer information om alla åtgärder som körs av tjänsten för användaretablering, inklusive etableringsstatus för enskilda användare (se avsnittet [Använd etableringsloggar](#use-provisioning-logs-to-check-a-users-provisioning-status) nedan).

När en **etableringscykel** har slutförts visar avsnittet Statistik hittills det ackumulerade antalet användare och grupper som hittills har etablerats, tillsammans med slutdatum och varaktighet för den senaste cykeln. **Aktivitets-ID:et** identifierar unikt den senaste etableringscykeln. **Jobb-ID** är en unik identifierare för etableringsjobbet och är specifikt för appen i din klientorganisation.

Etableringsstatusen kan visas i Azure-portalen på **fliken &gt; \[\] &gt; Etablera etablering av Azure Active Directory &gt; Enterprise Apps.**

![Förloppsindikator för etablering av sidan](./media/application-provisioning-when-will-provisioning-finish-specific-user/provisioning-progress-bar-section.png)

## <a name="use-provisioning-logs-to-check-a-users-provisioning-status"></a>Använda etableringsloggar för att kontrollera en användares etableringsstatus

Om du vill se etableringsstatus för en vald användare läser du [etableringsloggarna (förhandsversionen)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) i Azure AD. Alla åtgärder som körs av tjänsten för användaretablering registreras i Azure AD-etableringsloggarna. Detta inkluderar alla läs- och skrivåtgärder som görs till käll- och målsystemen och användardata som lästes eller skrevs under varje åtgärd.

Du kan komma åt etableringsloggarna i Azure-portalen genom att välja **Azure Active Directory** &gt; **Enterprise Apps** &gt; **Provisioning loggar (förhandsversion)** i avsnittet **Aktivitet.** Du kan söka i etableringsdata baserat på namnet på användaren eller identifieraren i antingen källsystemet eller målsystemet. Mer information finns i [Etableringsloggar (förhandsgranskning)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). 

Etableringsloggarna registrerar alla åtgärder som utförs av etableringstjänsten, inklusive:

* Fråga Azure AD för tilldelade användare som är i omfång för etablering
* Fråga målappen för att det finns dessa användare
* Jämföra användarobjekten mellan systemet
* Lägga till, uppdatera eller inaktivera användarkontot i målsystemet baserat på jämförelsen

Mer information om hur du läser etableringsloggarna i Azure-portalen finns i [rapporteringsguiden för etablering](check-status-user-account-provisioning.md).

## <a name="how-long-will-it-take-to-provision-users"></a>Hur lång tid tar det att etablera användare?
När du använder automatisk användaretablering med ett program etablerar och uppdaterar Azure AD automatiskt användarkonton i en app baserat på saker som [användar- och grupptilldelning](../manage-apps/assign-user-or-group-access-portal.md) med ett regelbundet schemalagt tidsintervall, vanligtvis var 40:e minut.

Hur tid det tar för en viss användare att etableras beror huvudsakligen på om etableringsjobbet kör en inledande cykel eller en inkrementell cykel.

- För **den inledande cykeln**beror jobbtiden på många faktorer, inklusive antalet användare och grupper som är i omfattning för etablering och det totala antalet användare och grupper i källsystemet. Den första synkroniseringen mellan Azure AD och en app kan ta allt från 20 minuter till flera timmar, beroende på storleken på Azure AD-katalogen och antalet användare i omfånget för etablering. En omfattande lista över faktorer som påverkar den inledande cykelns prestanda sammanfattas senare i det här avsnittet.

- För **inkrementella cykler** efter den inledande cykeln tenderar jobbtiderna att vara snabbare (t.ex. inom 10 minuter), eftersom etableringstjänsten lagrar vattenstämplar som representerar tillståndet för båda systemen efter den inledande cykeln, vilket förbättrar prestandan för efterföljande synkroniseringar. Jobbtiden beror på antalet ändringar som har identifierats i etableringscykeln. Om det finns färre än 5 000 ändringar av användar- eller gruppmedlemskap kan jobbet avslutas inom en enda inkrementell etableringscykel. 

I följande tabell sammanfattas synkroniseringstider för vanliga etableringsscenarier. I dessa scenarier är källsystemet Azure AD och målsystemet är ett SaaS-program. Synkroniseringstiderna härleds från en statistisk analys av synkroniseringsjobb för SaaS-programmen ServiceNow, Workplace, Salesforce och G Suite.


| Konfiguration av scope | Användare, grupper och medlemmar i omfattning | Inledande cykeltid | Inkrementell cykeltid |
| -------- | -------- | -------- | -------- |
| Endast synkroniserade tilldelade användare och grupper |  < 1 000 |  < 30 minuter | < 30 minuter |
| Endast synkroniserade tilldelade användare och grupper |  1,000 - 10,000 | 142 - 708 minuter | < 30 minuter |
| Endast synkroniserade tilldelade användare och grupper |   10,000 - 100,000 | 1 170 - 2 340 minuter | < 30 minuter |
| Synkronisera alla användare och grupper i Azure AD |  < 1 000 | < 30 minuter  | < 30 minuter |
| Synkronisera alla användare och grupper i Azure AD |  1,000 - 10,000 | < 30 - 120 minuter | < 30 minuter |
| Synkronisera alla användare och grupper i Azure AD |  10,000 - 100,000  | 713 - 1 425 minuter | < 30 minuter |
| Synkronisera alla användare i Azure AD|  < 1 000  | < 30 minuter | < 30 minuter |
| Synkronisera alla användare i Azure AD | 1,000 - 10,000  | 43 - 86 minuter | < 30 minuter |


Endast för **konfigurationssynkroniseringen tilldelade användare och grupper**kan du använda följande formler för att bestämma de ungefärliga minsta och högsta förväntade inledande **cykeltiderna:**

    Minimum minutes =  0.01 x [Number of assigned users, groups, and group members]
    Maximum minutes = 0.08 x [Number of assigned users, groups, and group members] 
    
Sammanfattning av faktorer som påverkar den tid det tar att slutföra en **inledande cykel:**

- Det totala antalet användare och grupper som är i omfattning för etablering.

- Det totala antalet användare, grupper och gruppmedlemmar som finns i källsystemet (Azure AD).

- Om användare i omfång för etablering matchas mot befintliga användare i målprogrammet eller behöver skapas för första gången. Synkroniseringsjobb som alla användare skapas för första gången tar ungefär *dubbelt så lång tid* som synkroniseringsjobb som alla användare matchas med befintliga användare för.

- Antal fel i [etableringsloggarna](check-status-user-account-provisioning.md). Prestanda är långsammare om det finns många fel och etableringstjänsten har gått in i ett karantäntillstånd. 

- Begär hastighetsbegränsningar och begränsning som implementeras av målsystemet. Vissa målsystem implementerar begränsningar för begäranden och begränsning, vilket kan påverka prestanda under stora synkroniseringsåtgärder. Under dessa förhållanden kan en app som tar emot för många begäranden för snabbt sänka svarsfrekvensen eller stänga anslutningen. För att förbättra prestanda måste kopplingen justeras genom att inte skicka appbegäranden snabbare än appen kan bearbeta dem. Etableringsappar som skapats av Microsoft gör den här justeringen. 

- Antalet och storleken på tilldelade grupper. Det tar längre tid att synkronisera tilldelade grupper än att synkronisera användare. Både antalet och storleken på de tilldelade grupperna påverkar prestanda. Om ett program har [mappningar aktiverade för gruppobjektsynkronisering](customize-application-attributes.md#editing-group-attribute-mappings)synkroniseras gruppegenskaper som gruppnamn och medlemskap utöver användare. Dessa ytterligare synkroniseringar tar längre tid än att bara synkronisera användarobjekt.

- Om prestanda blir ett problem och du försöker etablera majoriteten av användare och grupper i din klientorganisation använder du omfångsfilter. Med omfångsfilter kan du finjustera de data som etableringstjänsten extraherar från Azure AD genom att filtrera bort användare baserat på specifika attributvärden. Mer information om omfångsfilter finns i [Attributbaserad programetablering med omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="next-steps"></a>Nästa steg
[Automatisera etablering av användare och avetablering till SaaS-program med Azure Active Directory](user-provisioning.md)
