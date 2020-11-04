---
title: Granska och hantera efterlevnad av principer
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder Azure Policy för att använda inbyggda principer för Azure Machine Learning för att se till att dina arbets ytor är kompatibla med dina krav.
author: jhirono
ms.author: jhirono
ms.date: 09/15/2020
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.openlocfilehash: 47a97bb8fbe52d3dde84afd77997b179a5c9248d
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93325492"
---
# <a name="audit-and-manage-azure-machine-learning-using-azure-policy"></a>Granska och hantera Azure Machine Learning med Azure Policy

[Azure policy](../governance/policy/index.yml) är ett styrnings verktyg som gör att du kan se till att Azure-resurserna är kompatibla med dina principer. Med Azure Machine Learning kan du tilldela följande principer:

* **Kundhanterad nyckel** : granska eller Använd om arbets ytor måste använda en kundhanterad nyckel.
* **Privat länk** : granska om arbets ytor använder en privat slut punkt för att kommunicera med ett virtuellt nätverk.

Principer kan ställas in i olika omfång, t. ex. på prenumerations-eller resurs grupps nivå. Mer information finns i Azure Policy- [dokumentationen](../governance/policy/overview.md).

## <a name="built-in-policies"></a>Inbyggda principer

Azure Machine Learning innehåller en uppsättning principer som du kan använda för vanliga scenarier med Azure Machine Learning. Du kan tilldela dessa princip definitioner till din befintliga prenumeration eller använda dem som grund för att skapa egna anpassade definitioner. En fullständig lista över de inbyggda principerna för Azure Machine Learning finns i [inbyggda principer för Azure Machine Learning](../governance/policy/samples/built-in-policies.md#machine-learning).

Använd följande steg för att visa de inbyggda princip definitionerna som är relaterade till Azure Machine Learning:

1. Gå till __Azure policy__ i [Azure Portal](https://portal.azure.com).
1. Välj __definitioner__.
1. I __typ__ väljer du _inbyggd_ och för __kategori__ väljer du __Machine Learning__.

Härifrån kan du välja princip definitioner för att visa dem. När du visar en definition kan du använda länken __tilldela__ för att tilldela principen till en bestämd omfattning och konfigurera parametrarna för principen. Mer information finns i [tilldela en princip – Portal](../governance/policy/assign-policy-portal.md).

Du kan också tilldela principer med hjälp av [Azure PowerShell](../governance/policy/assign-policy-powershell.md), [Azure CLI](../governance/policy/assign-policy-azurecli.md)och [mallar](../governance/policy/assign-policy-template.md).

## <a name="workspaces-encryption-with-customer-managed-key"></a>Kryptering av arbets ytor med kundhanterad nyckel

Styr huruvida arbets ytor ska krypteras med en kundhanterad nyckel (CMK) eller med hjälp av en Microsoft-hanterad nyckel för att kryptera mått och metadata. Mer information om hur du använder CMK finns i avsnittet [Azure Cosmos DB](concept-enterprise-security.md#azure-cosmos-db) i säkerhets artikeln för företag.

Om du vill konfigurera den här principen anger du bedömnings parametern till __audit__ eller __Deny__. Om det är inställt på __granskning__ kan du skapa arbets ytor utan en CMK och en varnings händelse skapas i aktivitets loggen.

Om principen är inställd på __neka__ kan du inte skapa en arbets yta om den inte anger en CMK. Försök att skapa en arbets yta utan CMK resulterar i ett fel som liknar `Resource 'clustername' was disallowed by policy` och skapar ett fel i aktivitets loggen. Princip identifieraren returneras också som en del av det här felet.

## <a name="workspaces-should-use-private-link"></a>Arbets ytor ska använda privat länk

Kontrollerar om arbets ytor ska använda Azure Private-länken för att kommunicera med Azure Virtual Network. Mer information om hur du använder privat länk finns i [Konfigurera privat länk för en arbets yta](how-to-configure-private-link.md).

Konfigurera den här principen genom att ange bedömnings parametern till __audit__. Om du skapar en arbets yta utan att använda en privat länk skapas en varnings händelse i aktivitets loggen.

## <a name="next-steps"></a>Nästa steg

* [Dokumentation om Azure Policy](../governance/policy/overview.md)
* [Inbyggda principer för Azure Machine Learning](policy-reference.md)
* [Arbeta med säkerhets principer med Azure Security Center](../security-center/tutorial-security-policy.md)