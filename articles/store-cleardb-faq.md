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
ms.openlocfilehash: ddbf1eb960a24cfaa8d09ab45b6febca07e6d504
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979916"
---
# <a name="faq-for-cleardb-mysql-databases-with-azure-app-service"></a>Vanliga frågor och svar om ClearDB MySql-databaser med Azure App Service
Den här vanliga frågor och svar på vanliga frågor om hur du använder och köper ClearDB MySQL-databaser för Azure Web Apps.

> [!IMPORTANT]
> Från och med den 13 juni 2018 övergick ClearDB Azure-baserade kunder som för närvarande faktureras av Microsoft till en direkt faktureringsmodell med ClearDB. Informationen i den här artikeln är nu föråldrad. Du kan inte längre skapa eller uppgradera en ClearDB-databas som har skapats i Azure.
>
> Mer information och nästa steg finns i [Ändringar i ClearDB-serviceplaner](https://w2.cleardb.net/important-change-of-billing-notice-for-all-azure-cleardb-service-plans/).

## <a name="what-options-do-i-have-for-mysql-on-azure"></a>Vilka alternativ har jag för MySQL på Azure?
Se [ClearDB](https://w2.cleardb.net/) för den senaste informationen om den tjänsten. ClearDB är en MySQL-värdtjänst och hanterar MySQL-infrastrukturen för dig. 

Du har flera andra alternativ för att vara värd för MySQL i Azure:
* [Azure Database for MySQL](https://azure.microsoft.com/services/mysql/)
* [MySQL-kluster som körs på en virtuell Azure-dator](https://github.com/azure/azure-quickstart-templates/tree/master/mysql-replication)
* [En instans av MySQL körs på en Virtuell Azure-dator](virtual-machines/windows/classic/mysql-2008r2.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)


## <a name="do-i-need-a-credit-card-for-the-web-app--mysql-template-in-the-azure-marketplace"></a>Behöver jag ett kreditkort för webbappen + MySQL-mallen på Azure Marketplace?
Detta beror på vilken typ av prenumeration du använder. Här är några vanliga prenumerationstyper:

* [Betala allteftersom du går:](https://azure.microsoft.com/offers/ms-azr-0003p/)Kräver ett kreditkort, och när du köper en betald MySQL-databas debiteras ditt kreditkort.
* [Kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/): Inkluderar krediter för användning med Microsoft Azure-tjänster men tillåter inte köp av resurser från tredje part. Om du vill köpa tjänster från tredje part eller en betald MySQL-databas måste du använda en prenumeration på kreditkortsaktiverade. För Webbappar kan du skapa en GRATIS ClearDB MySQL-databas.
* [MSDN-prenumeration](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/) och **MSDN Dev Test Pay as you go:** Liknar Free trial, en MSDN-prenumeration kräver att du har ett kreditkort för att köpa en betald MySQL-lösning från ClearDB.
* [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/): EA-kunder faktureras mot sin EA varje kvartal för alla sina Azure Marketplace-köp (tredje part) på en separat konsoliderad faktura. Du faktureras utanför det monetära åtagandet för alla marknadsplatsköp. Observera att Azure Store för närvarande inte är tillgängligt för kunder som är registrerade i Azerbajdzjan, Kroatien, Norge och Puerto Rico. 

## <a name="why-was-i-charged-350-for-a-web-app--mysql-from-the-azure-marketplace"></a>Varför debiterades jag 3,50 USD för en webbapp + MySQL från Azure Marketplace?
Standarddatabasalternativet är Titan, som är $3.50. Vi visar inte kostnaden när du skapar databasen och du kan av misstag köpa en databas som du inte hade för avsikt att göra. Vi försöker hitta ett sätt att förbättra upplevelsen, men tills dess måste du kontrollera alla dina valda prisnivåer för webbapp och databas innan du klickar på **Skapa** och starta distributionen av resurserna.

## <a name="i-am-running-mysql-on-my-own-azure-virtual-machine-can-i-connect-my-azure-web-app-to-my-database"></a>Jag kör MySQL på min egen virtuella Azure-dator. Kan jag ansluta min Azure-webbapp till min databas?
Ja. Du kan ansluta webbappen till databasen så länge som din Virtuella Azure-dator har gett fjärråtkomst till din webbapp. Mer information finns i [Installera MySQL på en virtuell dator](virtual-machines/windows/classic/mysql-2008r2.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="in-which-countriesregions-are-cleardb-premium-mysql-clusters-supported"></a>I vilka länder/regioner stöds ClearDB Premium MySQL-kluster?
ClearDB Premium MySQL-kluster är tillgängliga i alla Azure-regioner över hela världen med undantag för Indien, Australien, Södra Brasilien och Kina.

## <a name="can-i-create-a-new-cluster-prior-to-creating-a-database-with-cleardb-premium-cluster-solution"></a>Kan jag skapa ett nytt kluster innan jag skapar en databas med ClearDB premiumklusterlösning?
Nej, det går inte att skapa tomma ClearDB-kluster. Med Azure-portalen kan du skapa databaser i ett kluster, vilket kan skapa ett nytt kluster samtidigt.

## <a name="will-i-be-warned-if-i-try-to-delete-a-cleardb-mysql-database-that-is-in-use-by-one-of-my-applications"></a>Kommer jag att varnas om jag försöker ta bort en ClearDB MySQL-databas som används av ett av mina program?
Nej, Azure varnar dig inte om du tar bort ett marketplace-köp som ditt program är beroende av.

## <a name="which-regions-can-i-create-cleardb-databases-in"></a>Vilka regioner kan jag skapa ClearDB-databaser i?
Azure Marketplace är inte tillgängligt för kunder som är registrerade i Azerbajdzjan, Kroatien, Norge eller Puerto Rico. ClearDB är inte tillgängligt i dessa regioner.

## <a name="what-pricing-tier-should-i-choose-for-a-production-web-app-and-database"></a>Vilken prisnivå ska jag välja för en produktionswebbapp och databas?
Använd Basic eller en högre prisnivå för Webbappar. För ClearDB rekommenderar vi antingen en Saturnus- eller Jupiterplan. Granska funktionerna & begränsningar för varje prisnivå för både [Web Apps](https://azure.microsoft.com/pricing/details/app-service/) och [ClearDB MySQL-databaser](https://w2.cleardb.net/important-change-of-billing-notice-for-all-azure-cleardb-service-plans/) för att välja den som passar dina behov.

## <a name="how-do-i-upgrade-my-cleardb-database-from-one-plan-to-another"></a>Hur uppgraderar jag min ClearDB-databas från ett abonnemang till ett annat?
I [Azure-portalen](https://portal.azure.com)kan du skala upp en ClearDB delad värddatabas. Läs den här [artikeln](https://blogs.msdn.microsoft.com/appserviceteam/2016/10/06/upgrade-your-cleardb-mysql-database-in-azure-portal/) om du vill veta mer. Vi stöder för närvarande inte uppgradering för ClearDB Premium-kluster i Azure-portalen.

## <a name="i-cant-see-my-cleardb-database-in-azure-portal"></a>Jag kan inte se min ClearDB-databas i Azure-portalen?
Om du har skapat en ClearDB-databas i klassiskt kan du inte se databasen i [Azure-portalen](https://portal.azure.com). Det finns ingen lösning för det här scenariot.

## <a name="who-do-i-contact-for-support-when-my-database-is-down"></a>Vem kontaktar jag för support när min databas är nere?
Kontakta [ClearDB-stöd](https://www.cleardb.com/developers/help/support) för eventuella databasrelaterade problem. Var beredd på att förse dem med din Azure-prenumerationsinformation.

## <a name="can-i-create-additional-users-for-my-cleardb-mysql-database-cluster-solution"></a>Kan jag skapa ytterligare användare för min ClearDB MySQL-databasklusterlösning?
Nej. Du kan inte skapa ytterligare användare, men du kan skapa ytterligare databaser i ClearDB-databasklustret.  

## <a name="can-basicpro-series-databases-be-upgraded-in-place-similar-to-planetary-plans-today-on-cleardb-portal"></a>Kan Basic / Pro-serien databaser uppgraderas på plats liknar Planetary planer idag på ClearDB portal?
Ja, basic-seriens databaser kan uppgraderas på plats (Basic 60 till Basic 500). Pro-serien kan uppgraderas på plats (Pro 125 till Pro 1000) med undantag för Pro 60. Vi stöder inte uppgradering av Pro 60-databasen för närvarande. 

## <a name="when-i-migrate-my-resources-from-one-subscription-to-another-does-my-cleardb-mysql-database-get-migrated-as-well"></a>När jag migrerar mina resurser från en prenumeration till en annan, migreras min ClearDB MySQL-databas också?
När du utför resursmigrering över prenumerationer gäller vissa [begränsningar.](azure-resource-manager/management/move-support-resources.md) En ClearDB MySQL-databas är en tjänst från tredje part och migreras därför inte under Azure-prenumerationsmigrering. Om du inte hanterar migreringen av MySQL-databasen innan du migrerar Azure-resurser kan dina ClearDB MySQL-databaser inaktiveras. Migrera databaserna manuellt först och utför sedan Azure-prenumerationsmigrering för din webbapp. 

## <a name="i-hit-the-spending-limit-on-my-subscription-i-removed-the-limit-and-my-app-service-is-online-however-the-database-is-not-accessible-how-do-i-re-enable-the-cleardb-database"></a>Jag når utgiftsgränsen för min prenumeration. Jag tog bort gränsen och min App Service är online, men databasen är inte tillgänglig. Hur aktiverar jag ClearDB-databasen igen?
Kontakta [ClearDB-stöd](https://www.cleardb.com/developers/help/support) för att återaktivera databasen. Förse dem med din Azure-prenumerationsinformation och databasnamn.

## <a name="can-i-transfer-a-cleardb-database-from-a-credit-card-subscription-to-an-ea-subscription"></a>Kan jag överföra en ClearDB-databas från en kreditkortsprenumeration till en EA-prenumeration?
Befintliga ClearDB-databaser använder det kreditkort som är associerat med befintliga prenumerationer. Om du vill använda en EA-prenumeration måste du migrera dina data till en ny databas:

* Köp en ny ClearDB-databas med din EA-prenumeration.
* Migrera dina data till den nya databasen.
* Uppdatera programmet så att det använder den nya databasen.
* Ta bort din gamla ClearDB-databas.

När du skapar en ny webbapp med MySQL (ClearDB) eller skapar en MySQL-databas (ClearDB) avgör prenumerationen du väljer hur du ska betala för tjänsten. Med en EA-prenumeration blockerar vi inte anskaffningen av tredjepartstjänster som ClearDB i Azure-portalen. EA-abonnemang faktureras utanför Monetärt åtagande och faktureras kvartalsvis och i efterskott. EA-kunden måste ställa in en betalningsmetod, till exempel ett kreditkort för att betala för alla marknadsplatstjänster från tredje part.

## <a name="where-can-i-see-the-charges-for-cleardb-resources-in-an-ea-subscription"></a>Var kan jag se avgifterna för ClearDB-resurser i en EA-prenumeration?
För Direct EA-kunder visas Azure Marketplace-avgifter på Enterprise Portal. Observera att alla marknadsplatsköp och marknadsförbrukning faktureras utanför monetärt åtagande och faktureras kvartalsvis och i efterskott. EA-kunder måste betala direkt till tredjepartsleverantörerna och kan göra det genom att aktivera en betalningsmetod som ett kreditkort med sitt EA-konto.

Indirekta EA-kunder kan hitta sina Azure Marketplace-prenumerationer på sidan **Hantera prenumerationer** på Enterprise Portal, men prissättningen är dold. Kunderna bör kontakta sin LSP för information om Marketplace-avgifter.

Åtkomst till Azure Marketplace för tjänster från tredje part kan hanteras av dina EA Azure-registreringsadministratörer. De kan inaktivera eller återaktivera åtkomst till köp från tredje part från Store i Hantera konton och prenumerationer under avsnittet Konton i Enterprise Portal.

## <a name="who-do-i-contact-for-questions-about-my-bill-for-cleardb-services-in-my-ea-subscription"></a>Vem kontaktar jag för frågor om min faktura för ClearDB-tjänster i min EA-prenumeration?
Kontakta [Enterprise Kundsupport](https://aka.ms/AzureEntSupport) med avseende på fakturering under deras EA-registrering. EA Portals supportteam svarar på din fråga eller hjälper dig att lösa problemet.

## <a name="more-information"></a>Mer information
[Vanliga frågor och svar om Azure Marketplace](https://azure.microsoft.com/marketplace/faq/)

