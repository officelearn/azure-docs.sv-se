---
title: Azure VMware-lösningar (AVS) – övervaka molnets privata moln aktivitet
description: Beskriver den information som är tillgänglig för aktiviteter i Azure VMware-lösningen per AVS-miljö, inklusive aviseringar, händelser, uppgifter och granskning.
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/13/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5d6fd1b92db62ab7cc9edd47c601910b8148bb95
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77019680"
---
# <a name="monitor-vmware-solutions-avs-activity"></a>Övervaka aktiviteter för VMware-lösningar (AVS)

I AVS-aktivitets loggar får du en insikt i de åtgärder som utförs på AVS-portalen. Listan innehåller aviseringar, händelser, uppgifter och granskning. Använd aktivitets loggarna för att avgöra vem, när och vilka åtgärder som utfördes. Aktivitets loggar innehåller inte några Läs åtgärder som utförs av en användare.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-avs-portal"></a>Åtkomst till AVS-portalen

Få åtkomst till [AVS-portalen](access-cloudsimple-portal.md).

## <a name="activity-information"></a>Aktivitets information

Välj **aktivitet** på menyn på sidan för att komma åt aktivitets sidorna.

![Översikt över aktivitets Sidan](media/activity-page-overview.png)

Om du vill visa information om någon av aktiviteterna på sidan aktivitet väljer du aktiviteten. En informations panel öppnas till höger. Åtgärder i panelen beror på typen av aktivitet. Klicka på **X** för att stänga panelen.

Klicka på en kolumn rubrik för att sortera visningen. Du kan filtrera kolumner för att visa vissa värden. Hämta aktivitets rapporten genom att klicka på **Hämta som CSV** -ikon.

## <a name="alerts"></a>Aviseringar

Aviseringar är aviseringar om eventuella betydande aktiviteter i din AVS-miljö. Aviseringar innehåller händelser som påverkar fakturerings-eller användar åtkomst.

Om du vill bekräfta aviseringar och ta bort dem från listan väljer du en eller flera i listan och klickar på **Bekräfta**.

Följande kolumner med information är tillgängliga för aviseringar. Klicka på **Redigera kolumner** och Välj kolumner som du vill visa.

| Kolumn | Beskrivning |
------------ | ------------- |
| Aviserings typ | Aviserings kategori.|
| Tid | Tiden då aviseringen inträffade. |
| Allvarsgrad | Betydelsen av aviseringen.|
| Resursnamn | Namnet som tilldelats resursen, till exempel namnet på den privata AVS-molnet. |
| Resurstyp | Resurs kategori: molnets privata moln, moln rack. |
| Resurs-ID | Identifierare för resursen. |
| Beskrivning | Beskrivning av vad som utlöste aviseringen. |
| Godkänt | Indikerar om aviseringen har bekräftats. |

## <a name="events"></a>Events

Händelser visar användar-och system aktivitet på AVS-portalen. På sidan händelser visas den aktivitet som är associerad med en speciell resurs och påverkannas allvarlighets grad.

Följande kolumner med information är tillgängliga för aviseringar. Klicka på **Redigera kolumner** och Välj kolumner som du vill visa.

| Kolumn | Beskrivning |
------------ | ------------- |
| Tid | Datum och tid då händelsen inträffade. |
| Händelse typ | Numerisk kod som identifierar händelsen. |
| Allvarsgrad | Allvarlighets grad för händelse.|
| Resursnamn | Namnet som tilldelats resursen, till exempel namnet på den privata AVS-molnet. |
| Resurstyp | Resurs kategori: molnets privata moln, moln rack. |
| Beskrivning | Beskrivning av vad som utlöste aviseringen. |

## <a name="tasks"></a>Aktiviteter

Aktiviteter är moln aktiviteter i molnet som förväntas ta 30 sekunder eller mer att slutföra. (Aktiviteter som förväntas ta mindre än 30 sekunder rapporteras endast som händelser.) Öppna sidan aktiviteter för att spåra förloppet för aktiviteterna för ditt AVS-privata moln.

Följande kolumner med information är tillgängliga för aviseringar. Klicka på **Redigera kolumner** och Välj kolumner som du vill visa.

| Kolumn | Beskrivning |
------------ | ------------- |
| Aktivitets-ID | Unikt ID för uppgiften. |
| Åtgärd | Åtgärd som ska utföras av uppgiften. |
| Användare | Användaren har tilldelats för att slutföra uppgiften. |
| Resursnamn | Namn som tilldelats resursen. |
| Resurstyp | Resurs kategori: molnets privata moln, moln rack. |
| Resurs-ID | Identifierare för resursen. |
| Start | Start tid för aktiviteten. |
| Slut | Slut tid för aktiviteten. |
| Status | Aktuell uppgifts status. |
| Förfluten tid | Tid som aktiviteten tog att slutföra (om den har slutförts) eller håller på att tas (om den pågår). |
| Beskrivning | Uppgifts beskrivning. |

## <a name="audit"></a>Granska

Gransknings loggar håller reda på användar aktivitet. Du kan använda gransknings loggar för att övervaka användar aktivitet för alla användare.

Följande kolumner med information är tillgängliga för aviseringar. Klicka på **Redigera kolumner** och Välj kolumner som du vill visa.

| Kolumn | Beskrivning |
------------ | ------------- |
| Tid | Tid för gransknings posten. |
| Åtgärd | Åtgärd som ska utföras av uppgiften. |
| Användare | Användaren har tilldelats uppgiften. |
| Resursnamn | Namn som tilldelats resursen. |
| Resurstyp | Resurs kategori: molnets privata moln, moln rack. |
| Resurs-ID | Identifierare för resursen. |
| Resultat | Resultat av aktiviteten, till exempel **lyckad**. |
| Åtgången tid | Tid för att slutföra uppgiften. |
| Beskrivning | Beskrivning av åtgärden. |

## <a name="next-steps"></a>Nästa steg

* [Använda virtuella VMware-datorer i Azure](quickstart-create-vmware-virtual-machine.md)
* Lär dig mer om [moln privata moln](cloudsimple-private-cloud.md)
