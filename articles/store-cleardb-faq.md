---
title: "Vanliga frågor och svar för ClearDB MySql-databaser med Azure App Service | Microsoft Docs"
description: "Svar på vanliga frågor om hur du använder ClearDB MySQL-databaser med Azure App Service."
documentationcenter: php
services: 
author: sunbuild
manager: yochayk
editor: 
tags: mysql
ms.assetid: c2ed5e78-6d7d-4d0c-b7ee-a52ae41ceab8
ms.service: multiple
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2016
ms.author: sumuth
ms.openlocfilehash: b0fad1d151799f88c72cfe1359978d69cc1421c2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="faq-for-cleardb-mysql-databases-with-azure-app-service"></a>Vanliga frågor och svar om ClearDB MySql-databaser med Azure App Service
Dessa vanliga frågor svar på vanliga frågor om att använda och inköp ClearDB MySQL databaser till Azure Web Apps.

## <a name="what-options-do-i-have-for-mysql-on-azure"></a>Vilka alternativ finns för MySQL på Azure?
Har du flera alternativ:

* [ClearDB delade MySQL-databas](/marketplace/partners/cleardb/databases/)
* [ClearDB MySQL Premium kluster](/marketplace/partners/cleardb-clusters/cluster/)
* [MySQL-kluster som körs på en virtuell dator i Azure](https://github.com/azure/azure-quickstart-templates/tree/master/mysql-replication)
* [MySQL som körs på en Azure VM-instans](virtual-machines/windows/classic/mysql-2008r2.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

ClearDB är en MySQL som värd för tjänsten och hanterar MySQL-infrastruktur för dig. När du kör en egen MySQL-kluster eller en databas på en virtuell dator i Azure, måste du konfigurera MySQL-server och hålla det uppdaterat med korrigeringar.

## <a name="do-i-need-a-credit-card-for-the-web-app--mysql-template-in-the-azure-marketplace"></a>Måste jag ha ett kreditkort för webbprogrammet + MySQL-mall i Azure Marketplace?
Detta beror på vilken typ av prenumeration som du använder. Här följer några vanliga prenumerationstyper:

* [Betala per](/offers/ms-azr-0003p/): kräver en kreditkort och när du köper en betald MySQL-databas som ditt kreditkort debiteras.
* [Kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/): innehåller krediter för användning med Microsoft Azure-tjänster utan att tillåta köp av resurser från tredje part. Aktivera prenumeration om du vill köpa tjänster från tredje part eller en betald MySQL-databas måste du använda ett kreditkort. Du kan skapa en kostnadsfri ClearDB MySQL-databas för Web Apps.
* [MSDN-prenumeration](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/) och **testning i MSDN Dev betala per användning**: liknar kostnadsfri utvärderingsversion, MSDN-prenumeration måste du ha ett kreditkort för att köpa en betald MySQL-lösning från ClearDB.
* [Enterprise-avtal (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/): EA kunder debiteras mot sina EA varje kvartal för alla sina Azure Marketplace (tredjeparts)-inköp på en separat, sammanslagen faktura. Du debiteras utanför summa för alla inköp från marketplace. Observera att för tillfället Azure Store inte är tillgängligt för kunder som har registrerats i Azerbajdzjan, Kroatien, Norge och registreringen. 
* [DreamSpark](https://www.dreamspark.com/Product/Product.aspx?productid=99): du kan skapa LEDIGT ClearDB-databaser för webbprogram. Det finns ingen gräns för antalet lediga ClearDB MySQL-databaser som du kan skapa. Observera att gratisdatabaserna är inte ska användas för produktion webbprogram som den här tjänsten är endast avsett för utvärderingsversionen.

## <a name="why-was-i-charged-350-for-a-web-app--mysql-from-the-azure-marketplace"></a>Varför har jag debiteras $3.50 för en webbapp + MySQL från Azure Marketplace?
Standard-databasalternativet är Titan är $3.50. Vi Visa kostnaden under databasen skapas och av misstag kan du köpa en databas som du inte ville. Vi försöker hitta ett sätt att förbättra upplevelsen men fram till dess måste du kontrollera alla dina valda prisnivåer för webbappen och databasen innan du klickar på **skapa** och starta distributionen av resurser.

## <a name="i-am-running-mysql-on-my-own-azure-virtual-machine-can-i-connect-my-azure-web-app-to-my-database"></a>Jag använder MySQL på min egen virtuell dator i Azure. Kan jag ansluta min Azure-webbapp till databasen?
Ja. Du kan ansluta ditt webbprogram till din databas så länge din virtuella Azure-datorn har gett fjärråtkomst till ditt webbprogram. Mer information finns i [installera MySQL på en virtuell dator](virtual-machines/windows/classic/mysql-2008r2.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="in-which-countries-are-cleardb-premium-mysql-clusters-supported"></a>Där är länder ClearDB Premium MySQL-kluster stöds?
[ClearDB Premium MySQL-kluster](/marketplace/partners/cleardb-clusters/cluster/) är tillgängliga i alla Azure-regioner över hela världen med undantag för Indien, Australien, södra och Kina.

## <a name="can-i-create-a-new-cluster-prior-to-creating-a-database-with-cleardb-premium-cluster-solution"></a>Kan jag skapa ett nytt kluster innan du skapar en databas med ClearDB premium klusterlösning?
Nej, skapar tom ClearDB kluster stöds inte. Azure-portalen kan du skapa databaser i ett kluster, vilket kan skapa ett nytt kluster på samma gång.

## <a name="will-i-be-warned-if-i-try-to-delete-a-cleardb-mysql-database-that-is-in-use-by-one-of-my-applications"></a>Jag varnas om jag försöker ta bort en ClearDB MySQL-databas som används av en av Mina program?
Nej, Azure kommer varnar dig inte om du tar bort ett marketplace-inköp som programmet är beroende av.

## <a name="which-regions-can-i-create-cleardb-databases-in"></a>Vilka regioner kan jag skapa ClearDB databaser i?
Azure Marketplace är inte tillgänglig för kunder som har registrerats i Azerbajdzjan Kroatien, Norge eller registreringen. ClearDB är inte tillgänglig i dessa regioner.

## <a name="what-pricing-tier-should-i-choose-for-a-production-web-app-and-database"></a>Vilken prisnivå ska välja för ett webbprogram för produktion och databasen?
Använd Basic eller en högre prisnivå för Web Apps. För ClearDB rekommenderar vi en Saturnus eller Jupiter plan. Granska funktioner och begränsningar för varje prisnivå för både [Web Apps](https://azure.microsoft.com/pricing/details/app-service/) och [ClearDB MySQL-databaser](/marketplace/partners/cleardb/databases/) att välja det alternativ som passar dina behov.

## <a name="how-do-i-upgrade-my-cleardb-database-from-one-plan-to-another"></a>Hur uppgraderar jag ClearDB databasen från en plan till en annan?
I den [Azure-portalen](https://portal.azure.com), du kan skala upp ClearDB delad värd-databas. Läs [artikel](https://blogs.msdn.microsoft.com/appserviceteam/2016/10/06/upgrade-your-cleardb-mysql-database-in-azure-portal/) vill veta mer. För närvarande stöder inte vi uppgraderingen för ClearDB Premium kluster i Azure-portalen.

## <a name="i-cant-see-my-cleardb-database-in-azure-portal"></a>ClearDB-databas på Azure-portalen ser inte?
Om vi skapar ClearDB-databas med Azure Resource Manager eller [nya Azure-portalen](https://portal.azure.com), kommer inte att visas i den [gamla Azure-portalen](https://manage.windowsazure.com). Att arbete-runt problemet är att koppla databasen manuellt till webbprogrammet. På liknande sätt om skapa ClearDB databas i den [gamla portalen](https://manage.windowsazure.com) du kommer inte att kunna se databasen i den [nya Azure-portalen](https://portal.azure.com). Det finns inga arbete runt för det senare fallet.

## <a name="who-do-i-contact-for-support-when-my-database-is-down"></a>Vem jag kontakta för support när databasen är nere?
Kontakta [ClearDB stöd](https://www.cleardb.com/developers/help/support) för databasen till problemen. Var beredd på att ge dem med din Azure-prenumerationsinformation.

## <a name="can-i-create-additional-users-for-my-cleardb-mysql-database-cluster-solution"></a>Kan jag skapa ytterligare användare för min klusterlösning för ClearDB MySQL-databas?
Nej. Du kan inte skapa ytterligare användare, men du kan skapa nya databaser på databasen ClearDB-klustret.  

## <a name="can-basicpro-series-databases-be-upgraded-in-place-similar-to-planetary-plans-today-on-cleardb-portal"></a>Basic/Pro-serien databaserna kan uppgraderas lokalt liknar idag Hubble planer på ClearDB portal?
Ja, uppgradera Grundserie databaser kan vara lokalt (grundläggande 60 via grundläggande 500). Pro serier kan inte uppgraderas på plats (Pro 125 via Pro 1000) utom Pro 60. Vi stöder inte uppgradering Pro 60 databasen för närvarande. 

## <a name="when-i-migrate-my-resources-from-one-subscription-to-another-does-my-cleardb-mysql-database-get-migrated-as-well"></a>När jag migrera mina resurser från en prenumeration till en annan min ClearDB MySQL-databas migreras samt?
När du utför resursmigrering alla prenumerationer, vissa [begränsningar](app-service/app-service-move-resources.md) tillämpas. En ClearDB MySQL-databas är en tjänst från tredje part och därför migreras inte under migreringen av Azure-prenumeration. Om du inte hantera migreringen av MySQL-databasen innan du migrerar Azure-resurser, kan du inaktivera ClearDB MySQL-databaser. Migrera manuellt databaserna först och sedan utföra migrering på Azure-prenumeration för din webbapp. 

## <a name="i-hit-the-spending-limit-on-my-subscription-i-removed-the-limit-and-my-app-service-is-online-however-the-database-is-not-accessible-how-do-i-re-enable-the-cleardb-database"></a>Jag har nått utgiftsgränsen på min prenumeration. Jag har tagit bort gränsen och min App Service är online, men databasen inte är tillgänglig. Hur aktiverar jag ClearDB databasen igen?
Kontakta [ClearDB support](https://www.cleardb.com/developers/help/support) aktivera databasen igen. Ange dem med din Azure-prenumeration information och databasen namn.

## <a name="can-i-transfer-a-cleardb-database-from-a-credit-card-subscription-to-an-ea-subscription"></a>Kan jag överföra en ClearDB-databas från en kreditkort prenumeration till en EA-prenumeration?
Befintliga ClearDB-databaser använder kreditkort som är associerade med de befintliga prenumerationerna. Om du vill använda en EA-prenumeration måste du migrera dina data till en ny databas:

* Köp en ny ClearDB-databas med EA-prenumeration.
* Migrera dina data till den nya databasen.
* Uppdatera programmet så att den nya databasen.
* Ta bort den gamla ClearDB-databasen.

När du skapar ett nytt webbprogram med MySQL (ClearDB) eller skapa en MySQL-databas (ClearDB), anger den prenumeration som du väljer hur du betalar för tjänsten. Med en prenumeration på EA blockerar vi inte inköp av tjänsterna från tredje part, till exempel ClearDB i Azure-portalen. EA prenumerationer debiteras utanför summa och debiteras kvartalsvis och resterande. EA kunden skulle behöva konfigurera en betalningsmetod till exempel ett kreditkort för att betala för några tjänster från tredje part marketplace.

## <a name="where-can-i-see-the-charges-for-cleardb-resources-in-an-ea-subscription"></a>Var hittar jag avgifterna för ClearDB resurser i en EA-prenumeration?
För direkta EA kunder är Azure Marketplace-debiteringar synliga på Enterprise Portal. Observera att alla inköp från marketplace och förbrukning av debiteras utanför summa och debiteras kvartalsvis och resterande. EA kunder få betala direkt till från tredje part-leverantörer och kan göra det genom att aktivera en betalningsmetod till exempel ett kreditkort med deras EA-konto.

Indirekt EA-kunder kan hitta sina Azure Marketplace-prenumerationer på den **hantera prenumerationer** i Enterprise-portalen men priser är dold. Kunder bör kontakta sina LSP information om marketplace-debiteringar.

Åtkomst till Azure Marketplace för tjänster från tredje part kan hanteras av din registrering EA Azure-administratörer. De kan inaktivera eller återaktivera åtkomst till 3 part inköp från arkivet i Hantera konton och -prenumerationer under avsnittet konton i Enterprise Portal.

## <a name="who-do-i-contact-for-questions-about-my-bill-for-cleardb-services-in-my-ea-subscription"></a>Vem kontaktar jag för frågor om min faktura för ClearDB tjänster i min EA-prenumeration?
Kontakta [Enterprise kundsupport](http://aka.ms/AzureEntSupport) med avseende på fakturering under sin EA-registrering. Supportgrupp för EA Portal kommer svar på din fråga eller bidra till att lösa problemet.

## <a name="more-information"></a>Mer information
[Vanliga frågor om Azure Marketplace](/marketplace/faq/)

