---
title: Självstudie för åtkomst & program kontroller – Azure Security Center
description: I den här självstudien lär du dig hur du konfigurerar en just-in-Time-princip för VM-åtkomst och en princip för program kontroll.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/03/2018
ms.author: memildin
ms.openlocfilehash: 56dd74fba46aa8b79c94b1460996bb6edb1ff93f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "90904605"
---
# <a name="tutorial-protect-your-resources-with-azure-security-center"></a>Självstudie: Skydda dina resurser ed Azure Security Center
Security Center begränsar din exponering för hot med kontroller för åtkomst och program för att blockera skadlig aktivitet. Just-in-Time (JIT)-åtkomsten för virtuella datorer minskar exponeringen för attacker genom att du kan neka beständiga åtkomst till virtuella datorer. Istället tillhandahåller du kontrollerad och granskad åtkomst till virtuella datorer enbart när det behövs. Anpassningsbara programkontroller hjälper till att skydda virtuella datorer mot skadlig programvara genom att kontrollera vilka program du kan köra på dina virtuella datorer. Security Center använder Machine Learning för att analysera processerna som körs på den virtuella datorn och hjälper dig att tillämpa Tillåt-List regler med hjälp av den här intelligensen.

I den här guiden får du lära du dig hur man:

> [!div class="checklist"]
> * Konfigurera en just-in-Time-princip för VM-åtkomst
> * Konfigurera en princip för programkontroll

## <a name="prerequisites"></a>Krav
Om du vill gå igenom de funktioner som beskrivs i den här självstudien måste du ha Azure Defender aktiverat. Du kan testa Azure Defender utan kostnad. Mer information finns i [testa Azure Defender](security-center-pricing.md).

## <a name="manage-vm-access"></a>Hantera åtkomst till virtuella datorer
JIT-åtkomst till virtuella datorer kan användas till att låsa inkommande trafik till dina virtuella Azure-datorer. Det här minskar exponeringen för attacker samtidigt som du enkelt kan ansluta till de virtuella datorerna när du behöver.

Hanteringsportar behöver inte vara öppna hela tiden. De behöver endast vara öppna medan du är ansluten till den virtuella datorn för att exempelvis utföra hantering eller underhåll. När just-in-Time har Aktiver ATS använder Security Center regler för nätverks säkerhets grupper (NSG) som begränsar åtkomsten till hanterings portar så att de inte kan nås av angripare.

Följ rikt linjerna i [skydda dina hanterings portar med just-in-Time-åtkomst](security-center-just-in-time.md).

## <a name="harden-vms-against-malware"></a>Förstärka virtuella mot skadlig programvara
Anpassningsbara programkontroller hjälper dig att definiera en uppsättning program som ska tillåtas att köras på konfigurerade resursgrupper, vilket bland annat hjälper dig skydda dina virtuella datorer mot skadlig kod. Security Center använder Machine Learning för att analysera processerna som körs på den virtuella datorn och hjälper dig att tillämpa Tillåt-List regler med hjälp av den här intelligensen.

Följ anvisningarna i [använda anpassningsbara program kontroller för att minska dina datorers angrepps ytor](security-center-adaptive-application.md).

## <a name="next-steps"></a>Nästa steg
I den här självstudien har du lärt dig att begränsa din exponering för hot genom att:

> [!div class="checklist"]
> * Konfigurera en just-in-Time-princip för VM-åtkomst för att tillhandahålla kontrollerad och granskad åtkomst till virtuella datorer endast vid behov
> * Konfigurera en princip för anpassningsbara programkontroller för att kontrollera vilka program som kan köras på din virtuella dator

Gå vidare till nästa kurs om du vill veta mer om att hantera säkerhetsincidenter.

> [!div class="nextstepaction"]
> [Självstudie: Reagera på säkerhetsincidenter](tutorial-security-incident.md)

<!--Image references-->
[1]: ./media/tutorial-protect-resources/just-in-time-vm-access.png
[2]: ./media/tutorial-protect-resources/add-port.png
[3]: ./media/tutorial-protect-resources/adaptive-application-control-options.png
[4]: ./media/tutorial-protect-resources/recommended-resource-groups.png
