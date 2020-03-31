---
title: Övervaka privat molnaktivitet
titleSuffix: Azure VMware Solution by CloudSimple
description: Beskriver informationen som är tillgänglig för aktivitet i Azure VMware-lösningen efter CloudSimple-miljö, inklusive aviseringar, händelser, uppgifter och granskning.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/13/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1228f3a54e02d4fe7a5133e2bfba55c38e34718a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77019680"
---
# <a name="monitor-vmware-solution-by-cloudsimple-activity"></a>Övervaka VMware-lösning efter CloudSimple-aktivitet

CloudSimple-aktivitetsloggar ger en inblick i åtgärder som utförs på CloudSimple-portalen.  Listan innehåller aviseringar, händelser, uppgifter och granskning.  Använd aktivitetsloggarna för att avgöra vem, när och vilka åtgärder som utfördes.  Aktivitetsloggar innehåller inga läsåtgärder som utförs av en användare.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure-portalen på [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Få åtkomst till CloudSimple-portalen

Öppna [CloudSimple-portalen](access-cloudsimple-portal.md).

## <a name="activity-information"></a>Aktivitetsinformation

Om du vill komma åt aktivitetssidorna väljer du **Aktivitet** på sidomenyn.

![Översikt över sidans aktivitet](media/activity-page-overview.png)

Om du vill visa information om någon av aktiviteterna på aktivitetssidan väljer du aktiviteten. En detaljpanel öppnas till höger. Åtgärderna i panelen beror på typen av aktivitet. Stäng panelen genom att klicka på **X.**

Klicka på en kolumnrubrik för att sortera skärmen.  Du kan filtrera kolumner för specifika värden som ska visas.  Ladda ner aktivitetsrapport genom att klicka på **Ladda ner som CSV-ikon.**

## <a name="alerts"></a>Aviseringar

Aviseringar är aviseringar om någon betydande aktivitet i din CloudSimple-miljö.  Aviseringar inkluderar händelser som påverkar fakturering eller användaråtkomst.

Om du vill bekräfta aviseringar och ta bort dem från listan markerar du en eller flera i listan och klickar på **Bekräfta**.

Följande informationskolumner är tillgängliga för aviseringar. Klicka på **Redigera kolumner** och markera kolumner som du vill visa.

| Kolumn | Beskrivning |
------------ | ------------- |
| Typ av varning | Kategori av varning.|
| Tid | När aviseringen inträffade. |
| Severity | Varningens betydelse.|
| Resursnamn | Namn som tilldelats resursen, till exempel namnet private cloud. |
| Resurstyp | Resurskategori: Privat moln, molnrack. |
| Resurs-ID | Identifierare för resursen. |
| Beskrivning | Beskrivning av vad som utlöste aviseringen. |
| Bekräftad | Uppgift om huruvida varningen är bekräftad. |

## <a name="events"></a>Händelser

Händelser visar användar- och systemaktivitet på CloudSimple-portalen. På sidan Händelser visas den aktivitet som är associerad med en viss resurs och hur allvarlig påverkan är.

Följande informationskolumner är tillgängliga för aviseringar. Klicka på **Redigera kolumner** och markera kolumner som du vill visa.

| Kolumn | Beskrivning |
------------ | ------------- |
| Tid | Datum och tid då händelsen inträffade. |
| Händelsetyp | Numerisk kod som identifierar händelsen. |
| Severity | Allvarlighetsgrad för händelser.|
| Resursnamn | Namn som tilldelats resursen, till exempel namnet private cloud. |
| Resurstyp | Resurskategori: Privat moln, molnrack. |
| Beskrivning | Beskrivning av vad som utlöste aviseringen. |

## <a name="tasks"></a>Aktiviteter

Uppgifter är privata molnaktiviteter som förväntas ta 30 sekunder eller mer att slutföra. (Aktiviteter som förväntas ta mindre än 30 sekunder rapporteras endast som händelser.) Öppna sidorna Uppgifter för att spåra förloppet för uppgifter för ditt privata moln.

Följande informationskolumner är tillgängliga för aviseringar. Klicka på **Redigera kolumner** och markera kolumner som du vill visa.

| Kolumn | Beskrivning |
------------ | ------------- |
| Aktivitets-ID | Unik identifierare för aktiviteten. |
| Åtgärd | Åtgärd som uppgiften utför. |
| Användare | Användaren har tilldelats för att slutföra uppgiften. |
| Resursnamn | Namn som tilldelats resursen. |
| Resurstyp | Resurskategori: Privat moln, molnrack. |
| Resurs-ID | Identifierare för resursen. |
| Start | Starttid för aktiviteten. |
| Slut | Sluttid för aktiviteten. |
| Status | Aktuell aktivitetsstatus. |
| Förfluten tid | Tid som aktiviteten tog att slutföra (om den är klar) eller som för närvarande tar (om pågående). |
| Beskrivning | Uppgiftsbeskrivning. |

## <a name="audit"></a>Granska

Granskningsloggar håller reda på användaraktivitet. Du kan använda granskningsloggar för att övervaka användaraktivitet för alla användare.

Följande informationskolumner är tillgängliga för aviseringar. Klicka på **Redigera kolumner** och markera kolumner som du vill visa.

| Kolumn | Beskrivning |
------------ | ------------- |
| Tid | Tidpunkten för granskningsposten. |
| Åtgärd | Åtgärd som uppgiften utför. |
| Användare | Användare som tilldelats uppgiften. |
| Resursnamn | Namn som tilldelats resursen. |
| Resurstyp | Resurskategori: Privat moln, molnrack. |
| Resurs-ID | Identifierare för resursen. |
| Resultat | Resultatet av aktiviteten, till exempel **Framgång**. |
| Tidsåtgång | Dags att slutföra uppgiften. |
| Beskrivning | Beskrivning av åtgärden. |

## <a name="next-steps"></a>Nästa steg

* [Använda virtuella VMware-datorer på Azure](quickstart-create-vmware-virtual-machine.md)
* Läs mer om [privata moln](cloudsimple-private-cloud.md)
