---
title: Vanliga frågor och svar om ClearDB MySql-databaser med Azure App Service
description: Svar på vanliga frågor om hur du använder ClearDB MySQL-databaser med Azure App Service.
documentationcenter: php
services: mysql
author: sunbuild
manager: yochayk
tags: mysql
ms.service: multiple
ms.topic: article
ms.date: 10/27/2016
ms.author: sumuth
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 1838dbb627a6dec46f817101fc1d0cca0961ef77
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64574201"
---
# <a name="faq-for-cleardb-mysql-databases-with-azure-app-service"></a>Vanliga frågor och svar om ClearDB MySql-databaser med Azure App Service
HÄR får du svar på vanliga frågor om hur du använder och köpa ClearDB MySQL databaser för Azure Web Apps.

> [!IMPORTANT]
> Från och med 13 juni 2018 övergick ClearDB Azure-baserade kunderna debiteras för närvarande av Microsoft för den direkta faktureringsmodellen med ClearDB. Informationen i den här artikeln är nu inaktuellt. Du kommer inte längre att kunna skapa eller uppgradera en ClearDB-databas som har skapats i Azure.
>
> Mer information och nästa steg i [ändras till ClearDB-tjänstplaner](https://w2.cleardb.net/important-change-of-billing-notice-for-all-azure-cleardb-service-plans/).

## <a name="what-options-do-i-have-for-mysql-on-azure"></a>Vilka alternativ finns för MySQL på Azure?
Se [ClearDB](https://w2.cleardb.net/) för den senaste informationen om den tjänsten. ClearDB är en MySQL som värd för tjänsten och hanterar MySQL-infrastrukturen åt dig. 

Du har flera andra alternativ för att hantera MySQL i Azure:
* [Azure Database for MySQL](https://azure.microsoft.com/services/mysql/)
* [MySQL-kluster som körs på en virtuell Azure-dator](https://github.com/azure/azure-quickstart-templates/tree/master/mysql-replication)
* [Instans av MySQL som körs på en virtuell Azure-dator](virtual-machines/windows/classic/mysql-2008r2.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)


## <a name="do-i-need-a-credit-card-for-the-web-app--mysql-template-in-the-azure-marketplace"></a>Måste jag ha ett kreditkort för webbprogram + MySQL-mall i Azure Marketplace?
Detta beror på vilken typ av prenumeration som du använder. Här följer några vanliga prenumerationstyper:

* [Betala](https://azure.microsoft.com/offers/ms-azr-0003p/): Kräver ett kreditkort och när du köper en betald MySQL-databas som ditt kreditkort debiteras.
* [Kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/): Omfattar krediter för användning med Microsoft Azure-tjänster, men tillåter inte köp av resurser från tredje part. Aktiverat prenumeration om du vill köpa tjänster från tredje part eller en betald MySQL-databas måste du använda ett kreditkort. Du kan skapa en kostnadsfri ClearDB MySQL-databas för Web Apps.
* [MSDN-prenumeration](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/) och **MSDN Dev-Test betala**: Liknar kostnadsfri utvärderingsversion, MSDN-prenumeration måste du ha ett kreditkort för att köpa en betald MySQL-lösning från ClearDB.
* [Enterprise-avtal (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/): EA-kunder debiteras mot deras EA varje kvartal för alla sina Azure Marketplace-köp (från tredje part) på en separat, konsoliderade faktura. Du debiteras utanför den summa i förskott för alla marketplace-köp. Observera att för tillfället Azure Store inte är tillgängligt för kunder som har registrerats i Azerbajdzjan, Kroatien, Norge och Puerto Rico. 

## <a name="why-was-i-charged-350-for-a-web-app--mysql-from-the-azure-marketplace"></a>Varför har jag debiterats $3.50 för en webbapp + MySQL från Azure Marketplace?
Standard-databasalternativet är Titan, vilket är $3.50. Vi Visa inte kostnaden under skapande av databas och du kan köpa en databas som du inte hade för avsikt att av misstag. Vi försöker hitta ett sätt att förbättra upplevelsen men fram till dess måste du kontrollera alla dina valda prisnivåer för webbappen och databasen innan du klickar på **skapa** och starta distributionen av resurser.

## <a name="i-am-running-mysql-on-my-own-azure-virtual-machine-can-i-connect-my-azure-web-app-to-my-database"></a>Jag använder MySQL på mitt eget Azure-dator. Kan jag ansluta min Azure-webbapp till min databas?
Ja. Du kan ansluta ditt webbprogram till din databas så länge som den virtuella Azure-datorn har gett fjärråtkomst till din webbapp. Mer information finns i [installera MySQL på en virtuell dator](virtual-machines/windows/classic/mysql-2008r2.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="in-which-countriesregions-are-cleardb-premium-mysql-clusters-supported"></a>Där är länder/regioner ClearDB MySQL för Premium-kluster stöds?
ClearDB MySQL för Premium-kluster är tillgängliga i alla Azure-regioner över hela världen med undantag för Indien, Australien, Brasilien, södra och Kina.

## <a name="can-i-create-a-new-cluster-prior-to-creating-a-database-with-cleardb-premium-cluster-solution"></a>Kan jag skapa ett nytt kluster innan du skapar en databas med ClearDB lösning för premium?
Nej, skapa tom ClearDB-kluster stöds inte. Azure-portalen kan du skapa databaser i ett kluster, vilket kan skapa ett nytt kluster på samma gång.

## <a name="will-i-be-warned-if-i-try-to-delete-a-cleardb-mysql-database-that-is-in-use-by-one-of-my-applications"></a>Får jag en varning om jag försöker ta bort en ClearDB MySQL-databas som används av en av Mina program?
Nej, Azure inte varnar dig om du tar bort en marketplace-köp som ditt program är beroende av.

## <a name="which-regions-can-i-create-cleardb-databases-in"></a>Vilka regioner kan jag skapa ClearDB-databaser i?
Azure Marketplace är inte tillgängligt för kunder som har registrerats i Azerbajdzjan, Kroatien, Norge och Puerto Rico. ClearDB är inte tillgänglig i dessa regioner.

## <a name="what-pricing-tier-should-i-choose-for-a-production-web-app-and-database"></a>Vilka prisnivå för en webbapp för produktion och databasen ska jag välja?
Använd Basic eller en högre prisnivå för Web Apps. ClearDB rekommenderar vi en Saturn- eller Jupiter-plan. Granska funktioner och begränsningar för varje prisnivå för både [Webbappar](https://azure.microsoft.com/pricing/details/app-service/) och [ClearDB MySQL-databaser](https://w2.cleardb.net/important-change-of-billing-notice-for-all-azure-cleardb-service-plans/) att välja det som passar dina behov.

## <a name="how-do-i-upgrade-my-cleardb-database-from-one-plan-to-another"></a>Hur uppgraderar jag min ClearDB-databas från en plan till en annan?
I den [Azure-portalen](https://portal.azure.com), du kan skala upp en ClearDB-delade värdbaserade databasen. Läs det här [artikeln](https://blogs.msdn.microsoft.com/appserviceteam/2016/10/06/upgrade-your-cleardb-mysql-database-in-azure-portal/) vill veta mer. För närvarande stöder inte vi uppgraderingen för ClearDB-Premium-kluster i Azure-portalen.

## <a name="i-cant-see-my-cleardb-database-in-azure-portal"></a>Jag kan inte se ClearDB-databasen i Azure-portalen?
Om du har skapat en ClearDB-databas i klassiskt läge kan du inte kan se din databas i den [Azure-portalen](https://portal.azure.com). Det finns inga arbets-runt det här scenariot.

## <a name="who-do-i-contact-for-support-when-my-database-is-down"></a>Vem kontaktar jag för support när min databas ligger nere?
Kontakta [ClearDB support](https://www.cleardb.com/developers/help/support) för en databas relaterad information. Vara beredd att förse dem med dina Azure-prenumerationsinformation.

## <a name="can-i-create-additional-users-for-my-cleardb-mysql-database-cluster-solution"></a>Kan jag skapa ytterligare användare för min lösning för ClearDB MySQL-databas?
Nej. Du kan inte skapa ytterligare användare men du kan skapa nya databaser i ditt kluster för ClearDB-databasen.  

## <a name="can-basicpro-series-databases-be-upgraded-in-place-similar-to-planetary-plans-today-on-cleardb-portal"></a>Basic/Pro-serien databaser vara kan uppgraderas på plats liknar Planetär planer idag på ClearDB-portalen?
Ja, uppgraderas Grundserie databaser kan vara på plats (grundläggande 60 via grundläggande 500). Pro-serien kan uppgraderas på plats (Pro 125 via Pro 1000) förutom Pro 60. Vi stöder inte uppgradering Pro 60 databasen för tillfället. 

## <a name="when-i-migrate-my-resources-from-one-subscription-to-another-does-my-cleardb-mysql-database-get-migrated-as-well"></a>När jag migrera mina resurser från en prenumeration till en annan min ClearDB MySQL-databas migreras också?
När du utför resursmigrering mellan prenumerationer, vissa [begränsningar](azure-resource-manager/resource-group-move-resources.md#app-service-limitations) gäller. En ClearDB MySQL-databas är en tjänst från tredje part och därför migreras inte under migreringen av Azure-prenumeration. Om du inte hantera migreringen av din MySQL-databas innan du migrerar Azure-resurser, kan ClearDB MySQL-databaser inaktiveras. Manuellt migrera dina databaser först och sedan utföra migrering för Azure-prenumeration för din webbapp. 

## <a name="i-hit-the-spending-limit-on-my-subscription-i-removed-the-limit-and-my-app-service-is-online-however-the-database-is-not-accessible-how-do-i-re-enable-the-cleardb-database"></a>Jag når utgiftsgränsen på min prenumeration. Jag har tagit bort gränsen och min App Service är online, men databasen inte är tillgänglig. Hur aktiverar jag ClearDB-databasen igen?
Kontakta [ClearDB support](https://www.cleardb.com/developers/help/support) aktivera databasen igen. Ange dem med namnet på din Azure-prenumeration information och databasen.

## <a name="can-i-transfer-a-cleardb-database-from-a-credit-card-subscription-to-an-ea-subscription"></a>Kan jag överföra en ClearDB-databas från en kreditkort prenumeration till ett EA-prenumeration?
Befintliga ClearDB-databaser använder kreditkort som är associerade med de befintliga prenumerationerna. Om du vill använda en EA-prenumeration måste du migrera dina data till en ny databas:

* Köpa en ny ClearDB-databas med EA-prenumerationen.
* Migrera dina data till den nya databasen.
* Uppdatera programmet så att den nya databasen.
* Ta bort den gamla ClearDB-databasen.

När du skapar en ny webbapp med MySQL (ClearDB) eller skapa en MySQL-databas (ClearDB), anger den prenumeration som du väljer hur du ska betala för tjänsten. Med en EA-prenumeration kan blockerar vi inte inköp av tjänster från tredje part, till exempel ClearDB i Azure-portalen. EA-prenumerationer faktureras utanför monetära åtaganden och faktureras kvartalsvis och i efterhand. EA-kund skulle behöva ställa in en betalningsmetod, till exempel ett kreditkort för att betala för någon tredje parts marketplace-tjänster.

## <a name="where-can-i-see-the-charges-for-cleardb-resources-in-an-ea-subscription"></a>Var hittar jag avgifterna för ClearDB-resurser i ett EA-prenumerationen?
Azure Marketplace-avgifter är synliga på Enterprise Portal för direkta EA-kunder. Observera att alla marketplace-köp och förbrukning faktureras utanför monetära åtaganden och faktureras kvartalsvis och i efterhand. EA-kunder betala direkt till tjänst från tredje part-leverantörer och göra det genom att aktivera en betalningsmetod, till exempel ett kreditkort med deras EA-konto.

Indirekta EA-kunder kan hitta sina Azure Marketplace-prenumerationer på den **hantera prenumerationer** i Enterprise-portalen men priser är dold. Kunder bör kontakta sin LSP information om marketplace-avgifter.

Åtkomst till Azure Marketplace för tjänster från tredje part kan hanteras av dina administratörer för Azure EA-registrering. De kan inaktivera eller återaktivera åtkomst till 3 part köp från Store i Hantera konton och prenumerationer under avsnittet konton i Enterprise Portal.

## <a name="who-do-i-contact-for-questions-about-my-bill-for-cleardb-services-in-my-ea-subscription"></a>Vem kontaktar jag för frågor om min faktura för ClearDB-tjänster i min EA-prenumeration?
Kontakta [Enterprise kundsupport](https://aka.ms/AzureEntSupport) för fakturering under deras EA-avtal. Supporten för EA-portalen ska svara på din fråga eller hjälpa dig att lösa problemet.

## <a name="more-information"></a>Mer information
[Vanliga frågor om Azure Marketplace](https://azure.microsoft.com/marketplace/faq/)

