---
title: Styra reserv fastigheten med Backup Center
description: Lär dig hur du styr din Azure-miljö för att se till att alla resurser är kompatibla från ett säkerhets kopierings perspektiv med Backup Center.
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: 009ee461b0372a3fb73ffb3b0ee5151b77bd8ef8
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90997665"
---
# <a name="govern-your-backup-estate-using-backup-center"></a>Styra reserv fastigheten med Backup Center

Backup Center hjälper dig att styra din Azure-miljö och se till att alla resurser är kompatibla från ett säkerhets kopierings perspektiv. Nedan visas några av styrnings funktionerna i säkerhets kopierings Center:

* Visa och tilldela Azure-principer för säkerhets kopiering

* Visa alla data källor som inte har kon figurer ATS för säkerhets kopiering.

## <a name="supported-scenarios"></a>Scenarier som stöds

* Se [support mat ris](backup-center-support-matrix.md) för en detaljerad lista över scenarier som stöds och som inte stöds.

## <a name="azure-policies-for-backup"></a>Azure-principer för säkerhets kopiering

Om du vill visa alla [Azure-principer](https://docs.microsoft.com/azure/governance/policy/overview) som är tillgängliga för säkerhets kopiering väljer du meny alternativet Azure- **principer för säkerhets kopiering** . Då visas alla inbyggda och anpassade [definitioner av Azure-principer för säkerhets kopiering](policy-reference.md) som är tillgängliga för tilldelning till dina prenumerationer och resurs grupper.

Genom att välja någon av definitionerna kan du [tilldela principen](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage#assign-a-policy) till ett definitions område.

![Välj Azure Policy definitioner](./media/backup-center-govern-environment/azure-policy-definitions.png)

## <a name="protectable-datasources"></a>Skydds bara data källor

Om du väljer meny alternativet **skydds bara data källor** kan du Visa alla dina data källor som inte har kon figurer ATS för säkerhets kopiering. Du kan filtrera listan efter DataSource-prenumeration, resurs grupp, plats, typ och taggar. När du har identifierat en data källa som behöver säkerhets kopie ras kan du högerklicka på motsvarande rutnäts objekt och välja **säkerhets kopiering** för att konfigurera säkerhets kopiering för resursen.

![Menyn skydds bara data källor](./media/backup-center-govern-environment/protectable-datasources.png)

## <a name="next-steps"></a>Nästa steg

* [Övervaka och hantera säkerhets kopior](backup-center-monitor-operate.md)
* [Utföra åtgärder med Backup Center](backup-center-actions.md)
* [Få insikter om dina säkerhets kopior](backup-center-obtain-insights.md)
