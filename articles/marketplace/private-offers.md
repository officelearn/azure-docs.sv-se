---
title: Privata erbjudanden | Azure
description: Privata erbjudanden på Azure Marketplace för appen och tjänsten utgivare.
services: Azure, Marketplace, Compute
documentationcenter: ''
author: qianw211
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 11/1/2018
ms.author: qianw211
ms.openlocfilehash: 1efe65feaac6e71437958451e8c1a44027495fce
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/29/2018
ms.locfileid: "52620021"
---
# <a name="private-offers"></a>Privata erbjudanden

Privata erbjudanden på [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/) aktivera utgivare kan skapa SKU: er som är bara synliga riktade kunder.

## <a name="unlock-enterprise-deals-with-private-offers"></a>Låsa upp enterprise-avtal med privata erbjudanden

Enterprise-kunder använda allt online marknadsplatser för att hitta, prova och köpa molnlösningar. Med privata erbjudanden använda utgivare nu marketplace privat dela anpassade lösningar med riktade kunder med funktioner som företag kräver:

- *Förhandlas priser* kan utgivare utöka rabatter och listan priser från offentligt tillgängliga erbjudanden.
- *Privata villkor* aktivera utgivare att skräddarsy allmänna villkor för en viss kund.
- *Specialiserade konfigurationer* kan utgivare skräddarsy sina virtuella datorer, Azure-program och SaaS-appar erbjudande till en enskild kundernas behov. Det här alternativet kan också utgivare att tillhandahålla förhandsversion åtkomst till nya produktfunktioner innan du startar bredare för alla kunder.

Privata erbjudanden kan utgivare kan dra nytta av skalan och globala tillgängligheten hos en offentliga marknaden med flexibilitet och kontroll som behövs för att förhandla och leverera anpassade erbjudanden och konfigurationer. De här funktionerna öppna tillsammans dörren till stark enterprise antagit molnbaserade marknadsplatser.  Företag kan nu köpa och sälja på sätt som de förväntar sig och begäran.

Privata erbjudanden är nu tillgängliga för den virtuella datorn, Azure-program (som lösningsmallar eller hanterade program) och SaaS-appar erbjuder. Som erbjuder offentliga, privata erbjudanden kan skapas och hanteras den [Cloud Partner Portal](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus).  Kunder kan beviljas eller nekas åtkomst till privata erbjudanden på några minuter.

## <a name="creating-private-offers-using-skus-and-plans"></a>Skapa privat erbjuder med hjälp av SKU: er och planer

För *nya eller befintliga erbjudanden med offentliga SKU: er eller planer*, utgivare kan enkelt skapa en ny privat variationer genom att skapa nya SKU: er eller planer och markera dem som privat.  [Privat SKU: er](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus) och planer som ingår i ett erbjudande och är bara synliga och att köpa riktade kunders. Privat SKU: er och planer kan återanvända Källavbildningen och/eller erbjuda metadata som redan har publicerats för en offentlig SKU eller plan. Det här alternativet kan utgivare kan skapa flera privata varianter av ett erbjudande till allmänheten utan att behöva publicera flera versioner av samma basavbildningen och erbjuder metadata. För virtuella datorer och Azure-program erbjuder endast, när en privat SKU delar en grundläggande avbildning med en offentlig SKU ändringar i erbjudandet basavbildningen kommer att sprida över alla offentliga och privata SKU: er med grundläggande avbildningen.

För *nya erbjudanden som endast innehåller privata SKU: er eller planer*, utgivare kan skapa sina erbjudanden som ett annat erbjudande och sedan markera SKU: er eller planer som privat. Erbjudanden som endast har privata SKU: er eller planer kan inte upptäcka eller tillgängliga öppet via [Azure Marketplace](https://azuremarketplace.microsoft.com) eller [Azure-portalen](https://azure.microsoft.com/features/azure-portal/) av kunder som inte är kopplad till erbjudandet.

## <a name="targeting-customers-with-private-offers"></a>Riktar in sig på kunder med privata erbjudanden
Utgivare kan riktas mot kunder som använder prenumerations-ID: n för både nya och befintliga privata erbjudanden. Utgivare med hjälp av ett erbjudande för virtuell dator eller Azure-program kan begränsa tillgängligheten för en privat SKU till en enskild Azure-prenumeration-ID eller ladda upp en CSV-fil med ID: N med upp till 20 000 Azure-prenumeration. När du använder ett privat SaaS-App-erbjudande, kan utgivare associera ett Azure-prenumeration-ID eller ett klient-ID om du vill begränsa tillgängligheten för en privat plan med hjälp av antingen manuell eller CSV ladda upp metoden.

När ett erbjudande har certifierats och publiceras, kan kunder uppdateras eller tas bort från SKU eller Plan inom några minuter med hjälp av funktionen synkronisering privat prenumerationer. Den här funktionen gör det möjligt för utgivare att snabbt och enkelt uppdatera listan över kunder som privat SKU eller planerar visas utan recertifying eller publicera erbjudandet.

## <a name="deploying-private-offers"></a>Distribuera privat erbjuder

Privata erbjudanden är bara synliga via den [Azure-portalen](https://azure.microsoft.com/features/azure-portal/) och inte visas via [Azure Marketplace](https://azuremarketplace.microsoft.com). När du har loggat in på Azure portal, kan kunderna välja Marketplace navigeringselement att få åtkomst till sina privata erbjudanden. Privata erbjudanden kommer också att visas i sökresultaten och kan distribueras via kommandoraden och Azure Resource Manager-mallar som andra erbjudanden.

![[Privata erbjudanden]](./media/marketplace-publishers-guide/private-offer.png)

Privata erbjudanden kommer också att visas i sökresultaten. Bara utkik efter ”privat” märket.

## <a name="next-steps"></a>Nästa steg

Om du vill dra nytta av de nya funktionerna kan du börja sälja på den [Azure Marketplace](https://azuremarketplace.microsoft.com/sell).
