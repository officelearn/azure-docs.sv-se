---
title: Använda administrationsportalen i Azure Stack | Microsoft Docs
description: Lär dig hur du använder administrationsportalen som en Azure Stack-operator.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 02/25/2019
ms.author: jeffgilb
ms.reviewer: efemmano
ms.lastreviewed: 02/25/2019
ms.openlocfilehash: 8662cfde881a90ce8f7fc6cceaa576d3b971d9d6
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56817940"
---
# <a name="quickstart-use-the-azure-stack-administration-portal"></a>Snabbstart: Använd Azure Stack-administrationsportalen

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

Det finns två portaler i Azure Stack; administrationsportalen och användarportalen (kallas ibland den *klient* portal.) Som operatör Azure Stack kan använda du administrationsportalen för dagliga hanteringen och driften av Azure Stack.

## <a name="access-the-administrator-portal"></a>Få åtkomst till administratörsportalen

Åtkomst till administratörsportalen kan bläddra till portalens URL och logga in med autentiseringsuppgifterna för Azure Stack-operatör. För ett integrerat system utifrån portalen URL varierar regionnamn och externa fullständigt kvalificerade domännamnet (FQDN) för Azure Stack-distributioner. Administrationsportalen URL: en är alltid detsamma för Azure Stack Development Kit (ASDK) distributioner. 

| Miljö | Portalwebbadress för administratör |   
| -- | -- | 
| ASDK| https://adminportal.local.azurestack.external  |
| Integrerade system | https://adminportal.&lt;*region*&gt;.&lt;*FQDN*&gt; | 
| | |

> [!TIP]
> För en ASDK-miljö måste du först se till att du kan [ansluta till development kit värden](azure-stack-connect-azure-stack.md) via anslutning till fjärrskrivbord eller ett virtuellt privat nätverk (VPN).

 ![Administrationsportalen](media/azure-stack-manage-portals/admin-portal.png)

Standardtidszon för alla Azure Stack-distributioner har angetts till Coordinated Universal Time (UTC). 

I administratörsportalen kan göra du saker som:

* [Registrera Azure Stack med Azure](azure-stack-registration.md)
* [Lägga till i marketplace](azure-stack-download-azure-marketplace-item.md)
* [Skapa planer, erbjudanden och prenumerationer för användare](azure-stack-plan-offer-quota-overview.md)
* [Övervaka hälsotillstånd och aviseringar](azure-stack-monitor-health.md)
* [Hantera uppdateringar för Azure Stack](azure-stack-updates.md)

Den **snabbsjälvstudien** panelen innehåller länkar till online-dokumentation för de vanligaste uppgifterna.

Men en operatör kan skapa resurser, till exempel virtuella datorer, virtuella nätverk och lagringskonton i administrationsportalen, du bör [logga in på användarportalen](user/azure-stack-use-portal.md) att skapa och testa resurser.

>[!NOTE]
>Den **skapa en virtuell dator** länk i panelen Snabbstart självstudiekursen har du skapa en virtuell dator i administrationsportalen, men detta är endast avsedd att verifiera att Azure Stack har distribuerats korrekt.

## <a name="understand-subscription-behavior"></a>Förstå beteendet för prenumeration

Det finns tre prenumerationer som skapas som standard i administrationsportalen; förbrukning, standardprovidern och Avläsning av programvara. Som operatör kan du förmodligen använder den *standard Providerprenumeration*. Du kan inte lägga till andra prenumerationer och använda dem i administrationsportalen. 

Andra prenumerationer har skapats av användare i användarportalen baserat på de planer och erbjudanden som du skapar för dessa. Användarportalen ger dock inte åtkomst till någon av de administrativa eller använda funktionerna i administrationsportalen.

Portaler för administration och användare backas upp av separata instanser av Azure Resource Manager. Inte på grund av den här Azure Resource Manager-uppdelning över prenumerationer portaler. Till exempel om du, som en Azure Stack-Operator, loggar in på användarportalen kan du inte komma åt den *standard Providerprenumeration*. Även om du inte har åtkomst till alla administrativa funktioner kan skapa du prenumerationer själv från offentliga erbjudandena. Så länge du är inloggad på användarportalen anses en klientanvändare.

  >[!NOTE]
  >I en ASDK miljö om en användare tillhör samma klientkatalog som Azure Stack-Operator, är de inte blockerad från loggar in på administrationsportalen. Men det går inte att de kommer åt något de administrativa uppgifter eller Lägg till prenumerationer för att få åtkomst till erbjudanden som är tillgängliga för dem i användarportalen.

## <a name="administration-portal-tips"></a>Portalen tips för administration

### <a name="customize-the-dashboard"></a>Anpassa instrumentpanelen

Instrumentpanelen innehåller en uppsättning standardpaneler som. Du kan välja **redigera instrumentpanel** att ändra standardinstrumentpanelen eller välj **ny instrumentpanel** att lägga till en anpassad instrumentpanel. Du kan enkelt lägga till paneler på en instrumentpanel. Du kan till exempel välja **+ skapa en resurs**, högerklicka på **erbjuder + planer**, och välj sedan **fäst på instrumentpanelen**.

Ibland kanske du ser en tom instrumentpanel i portalen. Om du vill återställa instrumentpanelen, klickar du på **redigera instrumentpanelen**, och högerklicka och välj **återställa till standardtillståndet**.

### <a name="quick-access-to-online-documentation"></a>Snabbåtkomst till online-dokumentation

För att komma åt Azure Stack-operatör-dokumentation, Använd hjälpen och stöd för ikonen (frågetecken) i det övre högra hörnet av administratörsportalen. Flytta markören till ikonen och välj sedan **hjälp + support**.

### <a name="quick-access-to-help-and-support"></a>Snabbåtkomst till hjälp och support

Om du väljer ikonen Hjälp och support (frågetecken) i det övre högra hörnet av administratörsportalen och välj sedan **ny supportbegäran**, sker något av följande resultat:

- Om du använder ett integrerat system, öppnar den här åtgärden en plats där du kan direkt öppna ett supportärende med Microsofts kundsupport (CSS). Referera till [var du kan få support](azure-stack-manage-basics.md#where-to-get-support) att förstå när du ska gå via Microsoft-supporten eller din leverantör maskinvarustöd OEM-tillverkare (original equipment manufacturer).
- Om du använder ASDK den här åtgärden öppnar den [Azure Stack forumwebbplatsen](https://social.msdn.microsoft.com/Forums/home?forum=AzureStack) direkt. Dessa forum övervakas regelbundet. Eftersom ASDK är en utvecklingsmiljö, det finns inget officiella stöd som erbjuds via Microsoft CSS.

### <a name="quick-access-to-the-azure-roadmap"></a>Snabb åtkomst till Azure-översikt

Om du väljer **hjälp och support** (frågetecken) i det övre högra hörnet av administrationen portalen och välj sedan **Azure-översikt**, en ny webbläsarflik öppnas och tar dig till Azure-översikten. Genom att skriva **Azure Stack** i den **produkter** sökrutan visas alla uppdateringar i Azure Stack-översikten.

## <a name="next-steps"></a>Nästa steg

[Registrera Azure Stack med Azure](azure-stack-registration.md) och fylla den [Azure Stack marketplace](azure-stack-marketplace.md) med objekt att erbjuda användarna. 
