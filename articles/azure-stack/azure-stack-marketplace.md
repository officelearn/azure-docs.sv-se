---
title: Publicera ett anpassat marketplace-objekt i Azure Stack (molnoperator) | Microsoft Docs
description: Lär dig hur du publicerar ett anpassat marketplace-objekt i Azure Stack som en Azure Stack-operator.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 60871cbb-eed2-433c-a76d-d605c7aec06c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2019
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 6c8a2cd746f1090e802f90a49f9a1f7469e98f5f
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2019
ms.locfileid: "54121538"
---
# <a name="azure-stack-marketplace-overview"></a>Översikt över Azure Stack Marketplace

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

Azure Stack Marketplace är en samling tjänster, program och resurser som är anpassade för Azure Stack. Resurser omfattar nätverk, virtuella datorer, lagring och mer. Om du Använd Marketplace för att skapa nya resurser och distribuera nya program; eller bläddra och välj de objekt som du vill använda. Om du vill använda ett Marketplace-objekt, måste användare prenumererar på ett erbjudande som ger åtkomst till objektet.

Som en Azure Stack-operatör du bestämma vilka objekt du lägger till (publicera) på Marketplace. Du kan publicera objekt, till exempel databaser, App Services och så vidare. Publicering gör dem synliga för alla användare. Du kan publicera anpassade objekt som du skapar, eller så kan publicera objekt från en växande [Azure Marketplace-objektlista](azure-stack-marketplace-azure-items.md). När du publicerar ett objekt på Marketplace kan kan användarna se dem inom fem minuter.

> [!CAUTION]  
> Alla artefakter för galleriet objekt, inklusive bilder och JSON-filerna är tillgängliga utan autentisering när du gör dem tillgängliga i Azure Stack Marketplace. Läs mer om överväganden när du publicerar anpassade marketplace-objekt, [skapa och publicera ett Marketplace-objekt](azure-stack-create-and-publish-marketplace-item.md).

Öppna i Marketplace i administratör portalen väljer **+ skapa en resurs**.

![Marketplace](media/azure-stack-publish-custom-marketplace-item/image1.png)

## <a name="marketplace-items"></a>Marketplace-objekt

Ett Azure Stack Marketplace-objekt är en tjänst, program eller resurs som användarna kan ladda ned och använda. Alla Azure Stack Marketplace-objekt är synliga för alla användare, inklusive administrativa objekt, till exempel planer och erbjudanden. De här objekten kräver inte en prenumeration för att visa, men är icke-funktionella till användare.

Varje Marketplace-objekt har:

* En Azure Resource Manager-mall för etablering.
* Metadata, till exempel strängar, ikoner och andra marknadsföringsmaterial.
* Formatera informationen för att visa objektet i portalen.

Alla objekt som publicerats på Marketplace använder Azure Gallery-paket (.azpkg)-formatet. Lägg till distribution eller runtime resurser (kod, zip-filer med programvara eller avbildningar av virtuella datorer) till Azure Stack separat, inte som en del av Marketplace-objekt.

Med version 1803 och senare konverterar Azure Stack avbildningar till sparse-filer när de hämtar från Azure eller när du laddar upp anpassade avbildningar. Den här processen lägger till tid när du lägger till en bild, men sparar utrymme och snabbar upp distributionen av dessa avbildningar. Konverteringen gäller endast för nya avbildningar. Befintlig avbildning ändras inte.

## <a name="next-steps"></a>Nästa steg

* [Hämta Marketplace-objekt](azure-stack-download-azure-marketplace-item.md)  
* [Skapa och publicera ett Marketplace-objekt](azure-stack-create-and-publish-marketplace-item.md)
