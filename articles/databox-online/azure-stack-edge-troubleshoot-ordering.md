---
title: Använd Azure Portal för att felsöka problem med Azure Stack Edge Pro-beställning | Microsoft Docs
description: Beskriver hur du felsöker problem med Azure Stack Edge Pro-beställning.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 08/11/2020
ms.author: alkohli
ms.openlocfilehash: be7d477d08b025fd450bfa859e8eb677ba680143
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96446378"
---
# <a name="troubleshoot-your-azure-stack-edge-pro-ordering-issues"></a>Felsök problem med Azure Stack Edge Pro-beställning

I den här artikeln beskrivs fel sökning av problem med Azure Stack Edge Pro-beställning.

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> * Felsöka beställnings problem

## <a name="unsupported-subscription-or-region"></a>Prenumeration eller region som inte stöds

**Fel Beskrivning:** Om du får ett fel i Azure Portal:

*Den valda prenumerationen eller regionen stöds inte. Välj en annan prenumeration eller region.*

![Prenumeration eller region som inte stöds](media/azure-stack-edge-troubleshoot-ordering/azure-stack-edge-troubleshoot-ordering-01.png)

**Föreslagen lösning:**  Se till att du har använt en prenumeration som stöds, till exempel [Microsoft Enterprise-avtal (EA)](https://azure.microsoft.com/overview/sales-number/), [Cloud Solution Provider (CSP)](/partner-center/azure-plan-lp)eller [Microsoft Azure-sponsring](https://azure.microsoft.com/offers/ms-azr-0036p/). Prenumerationer enligt principen betala per användning stöds inte. Mer information finns i [Azure Stack Edge Resource-krav](azure-stack-edge-deploy-prep.md#prerequisites).

Det finns en risk att Microsoft kan tillåta en uppgradering av prenumerations typ från fall till fall. Kontakta [Microsoft-supporten](https://azure.microsoft.com/support/options/) så att de kan förstå dina behov och justera dessa gränser på lämpligt sätt.

## <a name="selected-subscription-type-not-supported"></a>Den valda prenumerations typen stöds inte

**Fel:** Du har en EA-, CSP-eller sponsrad prenumeration och du får följande fel meddelande:

*Den valda prenumerations typen stöds inte. Se till att du använder en prenumeration som stöds. [Läs mer](azure-stack-edge-deploy-prep.md#prerequisites). Om du använder en prenumerations typ som stöds kontrollerar du att `Microsoft.DataBoxEdge` providern är registrerad. Information om hur du registrerar finns i [Registrera resurs leverantör](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers)*.

**Föreslagen lösning:** Följ de här stegen för att registrera Azure Stack Edge-resurs-providern:

1. I Azure Portal går du till **Start**  >  **prenumerationer**.

2. Välj den prenumeration som du vill använda för att beställa enheten.

3. Välj **resurs leverantörer** och Sök sedan efter **Microsoft. DataBoxEdge**.

    ![Registrera resursprovider](media/azure-stack-edge-troubleshoot-ordering/azure-stack-edge-troubleshoot-ordering-02.png)

Om du inte har ägar-eller deltagar åtkomst för att registrera resurs leverantören visas följande fel: *prenumerations &lt; prenumerationens namn &gt; har inte behörighet att registrera resurs leverantör (er): Microsoft. DataBoxEdge.*

Mer information finns i [Registrera resurs leverantörer](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers).

## <a name="microsoftdataboxedge-not-registered-for-subscription"></a>Microsoft. DataBoxEdge har inte registrerats för prenumeration

**Fel:** I Azure Portal väljer du en prenumeration som ska användas för Azure Stack Edge Pro eller Data Box Gateway och får följande fel meddelande:

*Resurs leverantör (er): Microsoft. DataBoxEdge har inte registrerats för prenumerations &lt; prenumerations namnet &gt; och du har inte behörighet att registrera en resurs leverantör för prenumerations &lt; prenumerations namnet &gt;*.

**Föreslagen lösning:** Öka prenumerations åtkomsten eller hitta någon med ägare eller deltagar åtkomst för att registrera resurs leverantören.

## <a name="resource-disallowed-by-policy"></a>Resursen tillåts inte enligt principen

**Fel:** I Azure Portal försöker du registrera en resurs leverantör och får följande fel meddelande:

*Resurs &lt; resurs namnet tilläts &gt; inte av en princip. (Kod: RequestDisallowedByPolicy). Initiativ: neka allmänt oönskade resurs typer. Princip: ej tillåtna resurs typer.*

**Föreslagen lösning:** Det här felet uppstår på grund av en befintlig Azure-princip som blockerar resurs skapandet. Azure-principer ställs in av en organisations system administratör för att säkerställa efterlevnad vid användning eller skapande av Azure-resurser. Om någon sådan princip blockerar Azure Stack Edge-resurs skapas, kontakta system administratören om du vill redigera din Azure-princip.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [fel sökning av problem med Azure Stack Edge-Pro](azure-stack-edge-troubleshoot.md).