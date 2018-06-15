---
title: Kontrollera trafik med Azure Network Watcher IP flödet verifiera - Azure-portalen | Microsoft Docs
description: Den här artikeln beskrivs hur du kontrollerar om trafik till eller från en virtuell dator tillåts eller nekas
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: e0e3e9a8-70eb-409a-a744-0ce9deb27148
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: b6d929f025c8b95709b7c0eb28ee78310e5f12a5
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2018
ms.locfileid: "31809954"
---
# <a name="check-if-traffic-is-allowed-or-denied-to-or-from-a-vm-with-ip-flow-verify-a-component-of-azure-network-watcher"></a>Kontrollera om trafik tillåts eller nekas till eller från en virtuell dator med IP-flöda Kontrollera en komponent i Azure Nätverksbevakaren

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-check-ip-flow-verify-portal.md)
> - [PowerShell](network-watcher-check-ip-flow-verify-powershell.md)
> - [CLI 1.0](network-watcher-check-ip-flow-verify-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-ip-flow-verify-cli.md)
> - [Azure REST-API](network-watcher-check-ip-flow-verify-rest.md)


IP-flöde verifiera är en funktion i Nätverksbevakaren som hjälper dig att kontrollera om tillåts trafik till eller från en virtuell dator. Verifieringen kan köras för inkommande eller utgående trafik. Det här scenariot är användbart för att hämta aktuella tillstånd om en virtuell dator kan kommunicera med en extern resurs eller en annan resurs. IP-flöde Kontrollera kan användas för att kontrollera om Nätverkssäkerhetsgrupp (NSG)-regler är korrekt konfigurerade och felsöka flöden som blockeras av NSG-regler. En annan orsak till att använda IP flödet Kontrollera är att kontrollera att trafik som du vill blockerade blockeras korrekt av NSG: N.

## <a name="before-you-begin"></a>Innan du börjar

Det här scenariot förutsätter att du redan har följt stegen i [skapa en Nätverksbevakaren](network-watcher-create.md) att skapa en Nätverksbevakaren eller har en befintlig instans av Nätverksbevakaren. Det här scenariot förutsätter att det finns en resursgrupp med en giltig virtuell dator som ska användas.

## <a name="scenario"></a>Scenario

Det här scenariot använder IP-flöda Kontrollera för att verifiera om en virtuell dator kan du kontakta en annan dator via port 443. Om trafiken nekas returnerar säkerhetsregeln som nekar trafiken. Läs mer om IP-flöda Kontrollera [flöda Kontrollera översikt över IP](network-watcher-ip-flow-verify-overview.md)

### <a name="run-ip-flow-verify"></a>Kontrollera kör IP-flöde

Navigera till din Nätverksbevakaren och på **IP-flöde Kontrollera**. Välj den virtuella datorn och nätverksgränssnittet som du vill kontrollera trafik från. Ange ytterligare filtrering information och klickar på **Kontrollera**.

När du klickar på **Kontrollera**, flödet baserat på kriterier som du angav är markerad. Resultatet är antingen **åtkomst tillåts** eller **åtkomst nekad**. Om åtkomst nekas tillhandahålls Nätverkssäkerhetsgrupp (NSG) och säkerhet regel som blockerar trafik. Om DOS-trafik är förväntat, lyckades regeln.

> [!NOTE]
> IP-flöde Kontrollera kräver att den Virtuella datorresursen allokeras.

Som du ser i följande bild, tilläts utgående HTTPS-trafiken.

![IP-flöde Kontrollera översikt][1]

Som det visas i följande bild, trafik ändras till inkommande och den inkommande porten ändras till 123. Trafik nu nekas meddelandet ”åtkomst nekad” har angetts tillsammans med gruppen och säkerhet nätverkssäkerhetsregeln som nekar trafik.

![IP-flöde resultat][2]

## <a name="next-steps"></a>Nästa steg

Om trafik blockeras och får inte vara, se [hantera Nätverkssäkerhetsgrupper](../virtual-network/manage-network-security-group.md) att spåra de grupp och säkerhet Nätverkssäkerhetsregler som har definierats.

[1]: ./media/network-watcher-check-ip-flow-verify-portal/figure1.png
[2]: ./media/network-watcher-check-ip-flow-verify-portal/figure2.png













