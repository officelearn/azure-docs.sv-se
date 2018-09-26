---
title: Ordna resurser med hanteringsgrupper i Azure
description: Läs om hanteringsgrupper och hur de används.
author: rthorn17
manager: rithorn
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 9/18/2018
ms.author: rithorn
ms.openlocfilehash: d031059f9811cedb703fec4920e00fd1b2e3f877
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47045365"
---
# <a name="organize-your-resources-with-azure-management-groups"></a>Ordna resurser med hanteringsgrupper i Azure

Om din organisation har många prenumerationer kan det behövas ett effektivt sätt att hantera åtkomst, principer och efterlevnad för prenumerationerna. Med Azures hanteringsgrupper får du en hanteringsnivå över prenumerationsnivån. Du kan ordna prenumerationerna i containrar som kallas hanteringsgrupper och tillämpa styrningsvillkor för hanteringsgrupperna. Alla prenumerationer i en hanteringsgrupp ärver automatiskt de villkor som tillämpas för hanteringsgruppen. Hanteringsgrupper tillhandahåller hantering i företagsklass i stor skala oavsett vilken typ av prenumeration du har.

Du kan till exempel tillämpa principer för en hanteringsgrupp som begränsar regionerna som är tillgängliga för att skapa virtuella datorer (VM). Den här principen tillämpas för alla hanteringsgrupper, prenumerationer och resurser under hanteringsgruppen genom att endast tillåta att virtuella datorer skapas i den regionen.

## <a name="hierarchy-of-management-groups-and-subscriptions"></a>Hierarki för hanteringsgrupper och prenumerationer

Du kan skapa en flexibel struktur för hanteringsgrupper och prenumerationer för att organisera dina resurser i en hierarki för enhetlig princip- och åtkomsthantering. Följande diagram visar ett exempel på att skapa en hierarki för styrning med hjälp av hanteringsgrupper.

![träd](./media/MG_overview.png)

Genom att skapa en hierarki som i det här exemplet kan du tillämpa en princip, till exempel VM-platser som är begränsade till regionen USA, västra för gruppen ”hanteringsgrupp för infrastrukturgruppen” för att aktivera interna efterlevnads- och säkerhetsprinciper. Den här principen ärvs av EA-prenumerationerna under den hanteringsgruppen och gäller även för alla virtuella datorer under dessa prenumerationer. Eftersom den här principen ärver från hanteringsgruppen till prenumerationerna kan den inte ändras av resursen eller prenumerationsägaren, vilket leder till bättre styrning.

Ett annat scenario där du kan använda hanteringsgrupper är för att ge användaråtkomst till flera prenumerationer. Genom att flytta flera prenumerationer under hanteringsgruppen kan du skapa en tilldelning av [rollbaserad åtkomstkontroll](../../role-based-access-control/overview.md) (RBAC) till den. Detta gör även att alla prenumerationer ärver åtkomsten.
Genom att tilldela till hanteringsgruppen kan du ge användarna åtkomst till allt de behöver utan att behöva skapa skript för RBAC-tilldelningar för flera prenumerationer.

### <a name="important-facts-about-management-groups"></a>Viktiga fakta om hanteringsgrupper

- 10 000 hanteringsgrupper kan användas i en enskild katalog.
- Ett träd för hanteringsgruppen har stöd för upp till sex nivåer.
  - Den här gränsen omfattar inte rotnivån eller prenumerationsnivån.
- Varje hanteringsgrupp och prenumeration har endast stöd för ett överordnat element.
- Varje hanteringsgrupp kan ha flera underordnade element.
- Alla prenumerationer och hanteringsgrupper ingår i en hierarki i varje katalog. I artikeln med [viktiga fakta om rothanteringsgruppen](#important-facts-about-the-root-management-group) kan du läsa mer om undantag i förhandsversionen.

## <a name="root-management-group-for-each-directory"></a>En rothanteringsgrupp för varje katalog

Varje katalog tilldelas en hanteringsgrupp på översta nivån som kallas för rothanteringsgruppen.
Rothanteringsgruppen är inbyggd i hierarkin så att alla hanteringsgrupper och prenumerationer är dess underordnade element. Med hjälp av rothanteringsgruppen kan globala principer och RBAC-tilldelningar tillämpas på katalognivå. [Katalogadministratören måste att utöka sin behörighet](../../role-based-access-control/elevate-access-global-admin.md) först för att bli rotgruppens ägare. När administratören är ägare av gruppen kan personen hantera hierarkin och tilldela önskad RBAC-roll till andra kataloganvändare eller grupper.

### <a name="important-facts-about-the-root-management-group"></a>Viktiga fakta om rothanteringsgruppen

- Rothanteringsgruppens namn och ID anges som standard. Visningsnamnet kan uppdateras när som helst på Microsoft Azure-portalen.
  - Namnet blir ”Rotgrupp för klienter”.
  - ID:t är samma som Azure Active Directory-ID:t.
- Rothanteringsgruppen kan inte flyttas eller tas bort, till skillnad från andra hanteringsgrupper.  
- Alla prenumerationer och hanteringsgrupper är underordnade rothanteringsgruppen i katalogen.
  - Alla resurser i katalogen är underordnade rothanteringsgruppen för global hantering.
  - Nya prenumerationer tilldelas som standard till rothanteringsgruppen när de skapas.
- Alla Azure-kunder kan se rothanteringsgruppen, men alla kunder får inte hantera rothanteringsgruppen.
  - Alla som har åtkomst till en prenumeration kan se kontexten för den aktuella prenumerationens placering i hierarkin.  
  - Det är inte någon som får åtkomst till rothanteringsgruppen som standard. Globala katalogadministratörer är de enda användarna som kan ge sig själva åtkomst.  Efter att ha fått åtkomst kan katalogadministratörerna tilldela önskade RBAC-roller till andra användare.  

> [!NOTE]
> Om du började använda tjänsten för hanteringsgrupper för din katalog före 2018-06-25 så kanske inte katalogen kan konfigureras med alla prenumerationer i hierarkin. Teamet för hanteringsgrupper uppdaterar retroaktivt alla kataloger som började använda hanteringsgrupper i den allmänt tillgängliga förhandsversionen före det datumet i juli/augusti 2018. Alla prenumerationer i katalogerna kommer att göras till underordnade under rothanteringsgruppen Prior (Före).
>
> Om du har frågor om den här retroaktiva processen kan du kontakta: managementgroups@microsoft.com  
  
## <a name="initial-setup-of-management-groups"></a>Initial konfiguration av hanteringsgrupper

När användarna börjar använda hanteringsgrupper måste de genomföra en initial konfigurationsprocess. Det första steget är att rothanteringsgruppen skapas i katalogen. När den här gruppen har skapats blir alla befintliga prenumerationer i katalogen underordnade rothanteringsgruppen. Den här processen är till för att kontrollera att det endast finns en hanteringsgrupphierarki i en katalog. Hierarkin i katalogen gör det möjligt för administrativa kunder att använda global åtkomst och principer som andra kunder i katalogen inte kan kringgå. Allt som tilldelas på roten gäller för alla hanteringsgrupper, prenumerationer, resursgrupper och resurser i katalogen genom att det finns en hierarki inom katalogen.

> [!IMPORTANT]
> Alla tilldelningar av användaråtkomst eller principtilldelning för rothanteringsgruppen **gäller för alla resurser inom katalogen**.
> Alla kunder bör därför utvärdera behovet av objekt som definierats för det här området.
> Användaråtkomst och principtilldelningar bör endast vara ”måsten” i den här omfattningen.  
  
## <a name="management-group-access"></a>Åtkomst till hanteringsgrupp

Azure-hanteringsgrupper har stöd för [Azures rollbaserade åtkomstkontroll (RBAC)](../../role-based-access-control/overview.md) för alla resursåtkomster och rolldefinitioner.
Dessa behörigheter ärvs av underordnade resurser som finns i hierarkin. Alla inbyggda RBAC-roller kan tilldelas till en hanteringsgrupp som ärver nedåt i hierarkin av resurserna.
RBAC-rollen VM-deltagare kan till exempel tilldelas till en hanteringsgrupp. Rollen har ingen åtgärd för hanteringsgruppen, men ärver av alla virtuella datorer under hanteringsgruppen.

Följande diagram visar listan över roller och åtgärder som stöds för hanteringsgrupper.

| RBAC-rollnamn             | Skapa | Byt namn | Flytta | Ta bort | Tilldela åtkomst | Tilldela princip | Läsa  |
|:-------------------------- |:------:|:------:|:----:|:------:|:-------------:| :------------:|:-----:|
|Ägare                       | X      | X      | X    | X      | X             | X             | X     |
|Deltagare                 | X      | X      | X    | X      |               |               | X     |
|MG-deltagare*             | X      | X      | X    | X      |               |               | X     |
|Läsare                      |        |        |      |        |               |               | X     |
|MG-läsare*                  |        |        |      |        |               |               | X     |
|Deltagare för resursprincip |        |        |      |        |               | X             |       |
|Administratör för användaråtkomst   |        |        |      |        | X             |               |       |

*: MG-deltagare och MG-läsare tillåter endast användare att utföra dessa åtgärder inom hanteringsgruppsomfånget.  

### <a name="custom-rbac-role-definition-and-assignment"></a>Definition och tilldelning av anpassad RBAC-roll

Anpassade RBAC-roller stöds inte för närvarande för hanteringsgrupper. Gå till [feedbackforumet för hanteringsgrupper](https://aka.ms/mgfeedback) om du vill se statusen för det här objektet.

## <a name="next-steps"></a>Nästa steg

Läs mer om hanteringslösningar här:

- [Skapa hanteringsgrupper för att organisera Azure-resurser](create.md)
- [Så här ändrar, raderar och hanterar du dina hanteringsgrupper](manage.md)
- [Installera Azure PowerShell-modulen](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups)
- [Granska specifikationerna för REST API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview)
- [Installera Azure CLI-tillägget](/cli/azure/extension?view=azure-cli-latest#az-extension-list-available)