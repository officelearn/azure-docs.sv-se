---
title: Med hjälp av administratörsportalen i Azure Stack | Microsoft Docs
description: Lär dig hur du använder administratörsportalen som Azure Stack-operatör.
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
ms.date: 09/12/2018
ms.author: mabrigg
ms.openlocfilehash: 975c5c2c5f0da7a6db946c6d1032b485947134d2
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "44717717"
---
# <a name="using-the-administrator-portal-in-azure-stack"></a>Med hjälp av administratörsportalen i Azure Stack

*Gäller för: integrerade Azure Stack-system och Azure Stack Development Kit*

Det finns två portaler i Azure Stack; administratörsportalen och användarportalen (kallas ibland den *klient* portal.) Du kan använda administratörsportalen för dagliga hanteringen och driften av Azure Stack som en Azure Stack-operator.

## <a name="access-the-administrator-portal"></a>Få åtkomst till administratörsportalen

För en utvecklingsmiljö kit, måste du först se till att du kan [ansluta till development kit värden](azure-stack-connect-azure-stack.md) via anslutning till fjärrskrivbord eller ett virtuellt privat nätverk (VPN).

Åtkomst till administratörsportalen kan bläddra till portalens URL och logga in med autentiseringsuppgifterna för Azure Stack-operatör. För ett integrerat system utifrån portalen URL varierar regionnamn och externa fullständigt kvalificerade domännamnet (FQDN) för Azure Stack-distributioner.

| Miljö | Portalwebbadress för administratör |   
| -- | -- | 
| Development kit| https://adminportal.local.azurestack.external  |
| Integrerade system | https://adminportal.&lt; *region*&gt;.&lt; *FQDN*&gt; | 
| | |

 ![Administratörsportalen](media/azure-stack-manage-portals/admin-portal.png)

I administratörsportalen kan göra du saker som:

* Hantera infrastrukturen (inklusive systemhälsa, uppdateringar, kapacitet, osv.)
* Lägga till i Marketplace
* Skapa prenumerationer för användare
* Skapa planer och erbjudanden

Den **snabbsjälvstudien** panelen innehåller länkar till online-dokumentation för de vanligaste uppgifterna.

Även om en operatör kan skapa resurser, till exempel virtuella datorer, virtuella nätverk och lagringskonton i administratörsportalen, bör du [logga in på användarportalen](user/azure-stack-use-portal.md) att skapa och testa resurser.

>[!NOTE]
>Den **skapa en virtuell dator** länk i panelen Snabbstart självstudiekursen har du skapa en virtuell dator i administratörsportalen, men detta är endast avsedd att verifiera Azure Stack när den först har distribuerats.

## <a name="understand-subscription-behavior"></a>Förstå beteendet för prenumeration

Det finns endast en prenumeration som är tillgängliga för användning från administratörsportalen. Den här prenumerationen har den *standard Providerprenumeration*. Du kan inte lägga till andra prenumerationer och använda dem i administratörsportalen.

Som en Azure Stack-operatör kan du lägga till prenumerationer för dina användare (inklusive dig själv) från administratörsportalen. Användare (inklusive dig själv) kan komma åt och använda dessa prenumerationer från den **användaren** portal. Användarportalen ger dock inte åtkomst till någon av de administrativa eller använda funktionerna i administratörsportalen.

Administratörs- och portaler backas upp av separata instanser av Azure Resource Manager. Inte på grund av den här Resource Manager-uppdelning över prenumerationer portaler. Till exempel om du, som Azure Stack-operatör, loggar in på användarportalen kan du inte komma åt den *standard Providerprenumeration*. Även om du inte har åtkomst till alla administrativa funktioner kan skapa du prenumerationer själv från offentliga erbjudandena. Så länge du är inloggad på användarportalen anses en klientanvändare.

  >[!NOTE]
  >I kit utvecklingsmiljö, om en användare tillhör samma klientkatalog som Azure Stack-operatör blockeras de inte logga in i administratörsportalen. Men de kan inte komma åt alla administrativa funktioner. Dessutom från administratörsportalen kan inte lägga till prenumerationer eller åtkomst erbjuder som är tillgängliga för dem i användarportalen.

## <a name="administrator-portal-tips"></a>Administratören portal tips

### <a name="customize-the-dashboard"></a>Anpassa instrumentpanelen

Instrumentpanelen innehåller en uppsättning standardpaneler som. Du kan välja **redigera instrumentpanel** att ändra standardinstrumentpanelen eller välj **ny instrumentpanel** att lägga till en anpassad instrumentpanel. Du kan enkelt lägga till paneler på en instrumentpanel. Du kan till exempel välja **+ skapa en resurs**, högerklicka på **erbjuder + planer**, och välj sedan **fäst på instrumentpanelen**.

### <a name="quick-access-to-online-documentation"></a>Snabbåtkomst till online-dokumentation

För att komma åt dokumentationen för Azure Stack-operator, Använd hjälpen och stöd för ikonen (frågetecken) i det övre högra hörnet av administratörsportalen. Flytta markören till ikonen och välj sedan **hjälp + support**.

### <a name="quick-access-to-help-and-support"></a>Snabbåtkomst till hjälp och support

Om du väljer ikonen Hjälp och support (frågetecken) i det övre högra hörnet av administratörsportalen och välj sedan **ny supportbegäran**, något av följande inträffa:

- Om du använder ett integrerat system, öppnar den här åtgärden en plats där du kan direkt öppna ett supportärende med Microsofts kundsupport (CSS). Referera till [var du kan få support](azure-stack-manage-basics.md#where-to-get-support) att förstå när du ska gå via Microsoft-supporten eller din leverantör maskinvarustöd OEM-tillverkare (original equipment manufacturer).
- Om du använder i development kit öppnas på Azure Stack-forumwebbplatsen direkt i den här åtgärden. Dessa forum övervakas regelbundet. Eftersom i development kit är en utvecklingsmiljö, det finns inget officiella stöd som erbjuds via Microsoft CSS.

## <a name="next-steps"></a>Nästa steg

- [Regionshantering i Azure Stack](azure-stack-region-management.md)
