---
title: Sent skede omprojection
description: Information om late stage reprojection och hur man använder den.
author: sebastianpick
ms.author: sepick
ms.date: 02/04/2020
ms.topic: article
ms.openlocfilehash: 4aa1148e544ff3451aa1cb956bc4a5fb932b9611
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680991"
---
# <a name="late-stage-reprojection"></a>Sent skede omprojection

*LSR (Late Stage Reprojection)* är en maskinvarufunktion som hjälper till att stabilisera hologram när användaren flyttar.

Statiska modeller förväntas visuellt behålla sin position när du flyttar runt dem. Om de verkar vara instabila kan det här beteendet antyder LSR-problem. Tänk på att ytterligare dynamiska omformningar, till exempel animeringar eller explosionsvyer, kan dölja detta beteende.

Du kan välja mellan två olika LSR-lägen, nämligen **Planar LSR** eller **Djup LSR**. Vilken som är aktiv avgörs av om klientprogrammet skickar en djupbuffert.

Båda LSR-lägena förbättrar hologramstabiliteten, även om de har sina distinkta begränsningar. Börja med att försöka Djup LSR, eftersom det är utan tvekan att ge bättre resultat i de flesta fall.

## <a name="choose-lsr-mode-in-unity"></a>Välj LSR-läge i Unity

Gå till *Fil > Build Settings i*Unity-redigeraren . Välj *Spelarinställningar* längst ned till vänster och kontrollera sedan under *Player > XR-inställningar > Virtual Reality-SDK:er > Windows Mixed Reality* om Aktivera **djupbuffertdelning** är markerat:

![Alternativ flagga för djupbuffertdelning](./media/unity-depth-buffer-sharing-enabled.png)

Om så är det använder appen Djup LSR, annars används Planar LSR.

## <a name="depth-lsr"></a>Djup LSR

För att djup LSR ska fungera måste klientprogrammet tillhandahålla en giltig djupbuffert som innehåller all relevant geometri att tänka på under LSR.

Djup LSR försöker stabilisera videoramen baserat på innehållet i den medföljande djupbufferten. Innehåll som inte har renderats till det, till exempel genomskinliga objekt, kan därför inte justeras av LSR och kan visa instabilitet och omprojectionartefakter.

## <a name="planar-lsr"></a>Planar LSR

Planar LSR har inte djupinformation per pixel, som Djup LSR gör. I stället projektets allt innehåll på nytt baserat på ett plan som du måste ange varje bildruta.

Planar LSR projekteter de objekt som bäst ligger nära det medföljande planet. Ju längre bort ett objekt är, desto mer instabilt kommer det att se ut. Djup LSR är bättre på att omprojektera objekt på olika djup, men Planar LSR kan fungera bättre för innehåll som är väl anpassat till ett plan.

### <a name="configure-planar-lsr-in-unity"></a>Konfigurera Planar LSR i Unity

Planet parametrar härleds från en så kallad *fokuspunkt*, `UnityEngine.XR.WSA.HolographicSettings.SetFocusPointForFrame`som du måste ge varje bildruta genom . Mer information finns i [API:et](https://docs.microsoft.com/windows/mixed-reality/focus-point-in-unity) för Unity Focus Point. Om du inte anger en fokuspunkt väljs en reservpunkt åt dig. Men att automatisk återgång leder ofta till suboptimala resultat.

Du kan beräkna fokuspunkten själv, men det kan vara klokt att basera den på den som beräknas av fjärråtergivningsvärden. Ring `RemoteManagerUnity.CurrentSession.GraphicsBinding.GetRemoteFocusPoint` för att få det. Du uppmanas att tillhandahålla en koordinatram där du kan uttrycka fokuspunkten. I de flesta fall vill du bara `UnityEngine.XR.WSA.WorldManager.GetNativeISpatialCoordinateSystemPtr` ge resultatet härifrån.

Vanligtvis både klienten och värden återge innehåll som den andra sidan inte är medveten om, till exempel gränssnittselement på klienten. Därför kan det vara meningsfullt att kombinera fjärrfokuspunkten med en lokalt beräknad.

Fokuspunkterna som beräknas i två på varandra följande ramar kan vara helt olika. Att bara använda dem som det är kan leda till hologram som verkar hoppa runt. För att förhindra detta är det tillrådligt att interpolera mellan föregående och aktuella fokuspunkter.

## <a name="next-steps"></a>Nästa steg

* [Prestandafrågor på serversidan](performance-queries.md)
