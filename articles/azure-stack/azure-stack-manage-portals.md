---
title: Med hjälp av administratörsportalen i Azure-stacken | Microsoft Docs
description: Som operatör Azure Stack lär du dig hur du använder administratörsportalen.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 02c7ff03-874e-4951-b591-28166b7a7a79
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2018
ms.author: mabrigg
ms.openlocfilehash: 673b1144fe927e0619f5f8638d7e8ce9a181f48c
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248528"
---
# <a name="using-the-administrator-portal-in-azure-stack"></a>Med hjälp av administratörsportalen i Azure-stacken

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Det finns två portaler i Azure-stacken; administratörsportalen och användarportalen (kallas ibland den *klient* portal.) Du kan använda administratörsportalen för dagliga driften och åtgärder i Azure-stacken som operatör Azure stacken.

## <a name="access-the-administrator-portal"></a>Åtkomst till administratörsportalen

För en development kit miljö måste du först se till att du kan [anslutas till development kit värden](azure-stack-connect-azure-stack.md) via anslutning till fjärrskrivbord eller ett virtuellt privat nätverk (VPN).

Åtkomst till administratörsportalen, bläddra till portal-URL och logga in med autentiseringsuppgifterna för en Azure-Stack-operator. För ett integrerat system baserat portalen URL varierar på regionnamn och externa fullständigt kvalificerade domännamnet (FQDN) för distributionen av Azure-stacken.

| Miljö | Administratören Portal-URL |   
| -- | -- | 
| Development kit| https://adminportal.local.azurestack.external  |
| Integrerade system | https://adminportal.&lt; *region*&gt;.&lt; *FQDN*&gt; | 
| | |

 ![Administratörsportalen](media/azure-stack-manage-portals/image1.png)

I administratörsportalen kan du göra saker som:

* hantera infrastrukturen (inklusive systemhälsa, uppdateringar, kapacitet, etc.)
* Lägga till i Marketplace
* Skapa prenumerationer för användare
* skapa planer och erbjudanden

Den **Snabbstartsguide** panelen innehåller länkar till online-dokumentation för de vanligaste uppgifterna.

Även om en operatör kan skapa resurser som virtuella datorer, virtuella nätverk och lagringskonton i administratörsportalen, bör du [logga in på användarportalen](user/azure-stack-use-portal.md) att skapa och testa resurser.

>[!NOTE]
>Den **skapa en virtuell dator** länk i panelen quickstart kursen har du skapa en virtuell dator i administratörsportalen, men detta är endast avsedd att validera Azure Stack efter den första har distribuerats.

## <a name="understand-subscription-behavior"></a>Förstå beteendet för prenumeration

Det finns bara en prenumeration som är tillgänglig i administratörsportalen. Den här prenumerationen är den *standard prenumeration på leverantör*. Du kan inte lägga till andra prenumerationer och använda dem i administratörsportalen.

Som operatör Azure Stack kan du lägga till prenumerationer för dina användare (inklusive dig själv) från administratörsportalen. Användare (inklusive dig själv) kan komma åt och använda dessa prenumerationer från den **användaren** portal. Användarportalen ger dock inte tillgång till någon av de administrativa eller använda funktionerna i administratörsportalen.

Portaler administratörs- och backas upp av separata instanser av Azure Resource Manager. Inte på grund av separationen Resource Manager över prenumerationer portaler. Till exempel om du, som operatör Azure Stack loggar in på användarportalen kan du inte komma åt den *standard prenumeration på leverantör*. Även om du inte har åtkomst till alla administrativa funktioner kan skapa du prenumerationer själv från offentliga erbjudandena. Så länge som du har loggat in på användarportalen anses en klient-användare.

  >[!NOTE]
  >I kit utvecklingsmiljö om en användare tillhör samma klient katalog som Azure Stack-operatorn blockeras de inte från att logga in till administratörsportalen. Men de kan inte komma åt alla administrativa funktioner. Dessutom från-administratörsportalen kan inte lägga till prenumerationer eller åtkomst erbjuder som är tillgängliga för dem i användarportalen.

## <a name="administrator-portal-tips"></a>Administratören portal tips

### <a name="customize-the-dashboard"></a>Anpassa instrumentpanelen

Instrumentpanelen innehåller en rad standardpaneler. Du kan välja **redigera instrumentpanel** ändra standardinstrumentpanelen eller välj **ny instrumentpanel** att lägga till en anpassad instrumentpanel. Du kan enkelt lägga till paneler till en instrumentpanel. Du kan till exempel välja **ny**, högerklicka på **erbjuder + planer**, och välj sedan **fäst på instrumentpanelen**.

### <a name="quick-access-to-online-documentation"></a>Snabb åtkomst till online-dokumentation

För att få åtkomst till Azure-stacken operator-dokumentation, använder Hjälp och stöder ikon (frågetecken) i övre högra hörnet i administratörsportalen. Flyttar markören till ikonen och välj sedan **hjälp + support**.

### <a name="quick-access-to-help-and-support"></a>Snabb åtkomst till hjälp och support

Om du väljer ikonen Hjälp och support (frågetecken) i övre högra hörnet i administratörsportalen och välj sedan **ny supportbegäran**, något av följande inträffa:

- Om du använder ett integrerat system kan öppnar den här åtgärden en plats där du kan direkt öppna ett supportärende med Microsofts kundsupport (CSS). Referera till [var du kan få support](azure-stack-manage-basics.md#where-to-get-support) att förstå när du ska gå via Microsoft-supporten eller leverantören för OEM-tillverkaren (OEM) maskinvarustöd.
- Om du använder i development kit öppnar Azure Stack forum platsen direkt i den här åtgärden. Dessa forum övervakas regelbundet. Eftersom development kit är en utvärderingsmiljö, stöds inte officiellt erbjuds via Microsoft CSS.

## <a name="next-steps"></a>Nästa steg

- [Regionhantering av i Azure-stacken](azure-stack-region-management.md)
