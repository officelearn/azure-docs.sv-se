---
title: "Med hjälp av administratörsportalen i Azure-stacken | Microsoft Docs"
description: "Som operatör Azure Stack lär du dig hur du använder administratörsportalen."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 02c7ff03-874e-4951-b591-28166b7a7a79
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: mabrigg
ms.openlocfilehash: 34d9d207225327758d535a11c870019f3db31cb0
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="using-the-administrator-portal-in-azure-stack"></a>Med hjälp av administratörsportalen i Azure-stacken

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Det finns två portaler i Azure-stacken; administratörsportalen och användarportalen (kallas ibland den *klient* portal). Du kan använda administratörsportalen för dagliga driften och åtgärder i Azure-stacken som operatör Azure stacken. 

## <a name="access-the-administrator-portal"></a>Åtkomst till administratörsportalen

För en development kit miljö måste du först se till att du kan [anslutas till development kit värden](azure-stack-connect-azure-stack.md) via anslutning till fjärrskrivbord eller ett virtuellt privat nätverk (VPN).

Åtkomst till administratörsportalen, bläddra till portal-URL och logga in med autentiseringsuppgifterna för en Azure-Stack-operator. För ett integrerat system baserat portalen URL varierar på regionnamn och externa fullständigt kvalificerade domännamnet (FQDN) för distributionen av Azure-stacken.

| Miljö | Administratören Portal-URL |   
| -- | -- | 
| Development kit| https://adminportal.Local.azurestack.external  |
| Integrerade system | https://adminportal. &lt; *region*&gt;.&lt; *FQDN*&gt; | 
| | |

 ![Administratörsportalen](media/azure-stack-manage-portals/image1.png)

I administratörsportalen kan du göra saker som:

* hantera infrastrukturen (inklusive systemhälsa, uppdateringar, kapacitet, etc.)
* Fyll i marketplace
* skapa planer och erbjudanden
* Skapa prenumerationer för användare

I den **Snabbstartsguide** panelen, det finns länkar till online-dokumentation för de vanligaste uppgifterna.
 
Även om det är möjligheten för en operator att skapa resurser, t.ex virtuella datorer, virtuella nätverk och lagringskonton i administratörsportalen, bör du [logga in på användarportalen](user/azure-stack-use-portal.md) att skapa och testa resurser. (Den **skapa en virtuell dator** länk i panelen quickstart kursen har du skapa en virtuell dator i administratörsportalen, men den här proceduren är endast för att verifiera Azure Stack efter första distributionen.)

## <a name="subscription-behavior"></a>Prenumerationen beteende
 
Det finns bara en prenumeration som är tillgängliga i administratörsportalen. Den här prenumerationen är den *standard prenumeration på leverantör*. Du kan inte lägga till andra prenumerationer för användning i administratörsportalen.

Som operatör Azure Stack kan du lägga till prenumerationer för dina användare (inklusive dig själv) från administratörsportalen. Användare (inklusive dig själv) kan komma åt och använda dessa prenumerationer från användarportalen. Användarportalen ger inte tillgång till någon av de administrativa eller använda funktionerna i administratörsportalen.

Portaler administratörs- och backas upp av separata instanser av Azure Resource Manager. Inte på grund av Resource Manager-åtskillnad över prenumerationer portaler. Om du som operatör Azure Stack loggar in på användarportalen använda du exempelvis inte standard providern prenumerationen. Därför kan har du inte åtkomst till alla administrativa funktioner. Du kan skapa prenumerationer själv från offentliga erbjudanden, men du betraktas som en klient-användare.

  >[!NOTE]
  I kit utvecklingsmiljö om en användare tillhör samma klient katalog som Azure Stack-operatorn blockeras de inte från att logga in till administratörsportalen. Men de kan inte komma åt alla administrativa funktioner. Dessutom från-administratörsportalen kan inte lägga till prenumerationer eller åtkomst erbjuder som görs tillgängliga för dem i användarportalen.

## <a name="administrator-portal-tips"></a>Administratören portal tips

### <a name="customize-the-dashboard"></a>Anpassa instrumentpanelen

Instrumentpanelen innehåller en rad standardpaneler. Du kan klicka på **redigera instrumentpanel** ändra standardinstrumentpanelen, eller klicka på **ny instrumentpanel** att lägga till anpassade instrumentpaneler. Du kan enkelt lägga till paneler på instrumentpanelen. Du kan till exempel klicka **ny**, högerklicka på **erbjuder + planer**, och klicka sedan på **fäst på instrumentpanelen**.

### <a name="quick-access-to-online-documentation"></a>Snabb åtkomst till online-dokumentation

Klicka på ikonen Hjälp och support (frågetecken) i övre högra hörnet i administratörsportalen för att få åtkomst till Azure-stacken operator-dokumentation, och klicka sedan på **hjälp + support**.

### <a name="quick-access-to-help-and-support"></a>Snabb åtkomst till hjälp och support

Om du klickar du på ikonen Hjälp och support (frågetecken) i övre högra hörnet i administratörsportalen och klicka sedan på **ny supportbegäran**, detta påverkar något av följande:

- Om du använder ett integrerat system kan öppnar den här åtgärden en plats där du kan direkt öppna ett supportärende med Microsofts kundsupport (CSS). Se avsnittet ”där du kan få support” av [grunderna i Azure-stacken administration](azure-stack-manage-basics.md) att förstå när du ska gå via Microsoft-supporten eller leverantören för OEM-tillverkaren (OEM) maskinvarustöd.
- Om du använder i development kit öppnar Azure Stack forum platsen direkt i den här åtgärden. Dessa forum övervakas regelbundet. Eftersom development kit är en utvärderingsmiljö, stöds inte officiellt erbjuds via Microsoft CSS.

## <a name="next-steps"></a>Nästa steg

- [Regionhantering av i Azure-stacken](azure-stack-region-management.md)
