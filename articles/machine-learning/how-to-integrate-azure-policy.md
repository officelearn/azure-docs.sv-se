---
title: Granska och hantera efterlevnad med Azure Policy
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder Azure Policy för att använda inbyggda principer för Azure Machine Learning.
author: jhirono
ms.author: jhirono
ms.date: 09/15/2020
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.openlocfilehash: e7eebb22efe32b290e078348337049c6c3e762db
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90997209"
---
# <a name="audit-and-manage-azure-machine-learning-using-azure-policy"></a>Granska och hantera Azure Machine Learning med Azure Policy

[Azure policy](/azure/governance/policy) är ett styrnings verktyg som gör att du kan se till att Azure-resurserna är kompatibla med dina principer. Med Azure Machine Learning kan du tilldela följande principer:

* **Kundhanterad nyckel**: granska eller Använd om arbets ytor måste använda en kundhanterad nyckel.
* **Privat länk**: granska om arbets ytor använder en privat slut punkt för att kommunicera med ett virtuellt nätverk.

Principer kan ställas in i olika omfång, t. ex. på prenumerations-eller resurs grupps nivå. Mer information finns i Azure Policy- [dokumentationen](/azure/governance/policy/overview).

## <a name="built-in-policies"></a>Inbyggda principer

Azure Machine Learning innehåller en uppsättning principer som du kan använda för vanliga scenarier med Azure Machine Learning. Du kan tilldela dessa princip definitioner till din befintliga prenumeration eller använda dem som grund för att skapa egna anpassade definitioner. En fullständig lista över de inbyggda principerna för Azure Machine Learning finns i [inbyggda principer för Azure Machine Learning](/azure/governance/policy/samples/built-in-policies#machine-learning).

Använd följande steg för att visa de inbyggda princip definitionerna som är relaterade till Azure Machine Learning:

1. Gå till __Azure policy__ i [Azure Portal](https://portal.azure.com).
1. Välj __definitioner__.
1. I __typ__väljer du _inbyggd_och för __kategori__väljer du __Machine Learning__.

Härifrån kan du välja princip definitioner för att visa dem. När du visar en definition kan du använda länken __tilldela__ för att tilldela principen till en bestämd omfattning och konfigurera parametrarna för principen. Mer information finns i [tilldela en princip – Portal](/azure/governance/policy/assign-policy-portal).

Du kan också tilldela principer med hjälp av [Azure PowerShell](/azure/governance/policy/assign-policy-powershell), [Azure CLI](https://docs.microsoft.com/azure/governance/policy/assign-policy-azurecli)och [mallar](/azure/governance/policy/assign-policy-template).

## <a name="workspaces-encryption-with-customer-managed-key"></a>Kryptering av arbets ytor med kundhanterad nyckel

Styr huruvida arbets ytor ska krypteras med en kundhanterad nyckel (CMK) eller med hjälp av en Microsoft-hanterad nyckel för att kryptera mått och metadata. Mer information om hur du använder CMK finns i avsnittet [Azure Cosmos DB](concept-enterprise-security.md#azure-cosmos-db) i säkerhets artikeln för företag.

Om du vill konfigurera den här principen anger du bedömnings parametern till __audit__ eller __Deny__. Om det är inställt på __granskning__kan du skapa arbets ytor utan en CMK och en varnings händelse skapas i aktivitets loggen.

Om principen är inställd på __neka__kan du inte skapa en arbets yta om den inte anger en CMK. Försök att skapa en arbets yta utan CMK resulterar i ett fel som liknar `Resource 'clustername' was disallowed by policy` och skapar ett fel i aktivitets loggen. Princip identifieraren returneras också som en del av det här felet.

## <a name="workspaces-should-use-private-link"></a>Arbets ytor ska använda privat länk

Kontrollerar om arbets ytor ska använda Azure Private-länken för att kommunicera med Azure Virtual Network. Mer information om hur du använder privat länk finns i [Konfigurera privat länk för en arbets yta](how-to-configure-private-link.md).

Konfigurera den här principen genom att ange bedömnings parametern till __audit__. Om du skapar en arbets yta utan att använda en privat länk skapas en varnings händelse i aktivitets loggen.

## <a name="next-steps"></a>Nästa steg

* [Dokumentation om Azure Policy](/azure/governance/policy/overview)
* [Inbyggda principer för Azure Machine Learning](policy-reference.md)
* [Arbeta med säkerhets principer med Azure Security Center](/azure/security-center/tutorial-security-policy)