---
title: Huvud versions uppgradering – Azure Portal-Azure Database for MySQL-enskild server
description: Den här artikeln beskriver hur du kan uppgradera huvud versionen för Azure Database for MySQL-en server med Azure Portal
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 11/16/2020
ms.openlocfilehash: 080d7c09b180d5943216793119718eb6a2add79e
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95753059"
---
# <a name="major-version-upgrade-in-azure-database-for-mysql-single-server-using-the-azure-portal"></a>Huvud versions uppgradering i Azure Database for MySQL enskild server med hjälp av Azure Portal

> [!IMPORTANT]
> Huvud versions uppgradering för Azure Database för MySQL Single Server finns i en offentlig för hands version.

Den här artikeln beskriver hur du kan uppgradera din MySQL-huvudversion på plats i Azure Database for MySQL enskild server.

Den här funktionen gör det möjligt för kunder att utföra uppgraderingar på plats av sina MySQL 5,6-servrar till MySQL 5,7 med ett klick på knappen utan någon data förflyttning eller behovet av eventuella anslutnings strängar för program.

> [!Note]
> * Huvud versions uppgradering är bara tillgänglig för uppgradering av högre version från MySQL 5,6 till MySQL 5,7<br>
> * Det finns inte stöd för att uppgradera större versioner på replik servern ännu.
> * Servern kommer inte att vara tillgänglig under uppgraderings åtgärden. Vi rekommenderar därför att du utför uppgraderingar under det planerade underhålls fönstret.

## <a name="prerequisites"></a>Förutsättningar
För att slutföra den här instruktions guiden behöver du:
- En [Azure Database for MySQL enskild server](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57"></a>Utför större versions uppgradering från MySQL 5,6 till MySQL 5,7

Följ de här stegen för att utföra större versions uppgradering för din Azure Database of MySQL 5,6-Server

> [!IMPORTANT]
> Vi rekommenderar att du först uppgraderar till en återställd kopia av servern i stället för att uppgradera produktion direkt. Se [hur du utför en tidpunkts återställning](howto-restore-server-portal.md#point-in-time-restore). 

1. I [Azure Portal](https://portal.azure.com/)väljer du din befintliga Azure Database for MySQL 5,6-Server.

2. På sidan **Översikt** klickar du på knappen **Uppgradera** i verktygsfältet.

3. I avsnittet **Uppgradera** väljer du **OK** för att uppgradera Azure Database for MySQL 5,6 server till 5,7 Server.

    :::image type="content" source="./media/how-to-major-version-upgrade-portal/upgrade.png" alt-text="Azure Database for MySQL-översikt – uppgradering":::

4. Ett meddelande bekräftar att uppgraderingen har slutförts.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**1. När kommer den här uppgraderings funktionen att vara GA när vi har MySQL v 5.6 i vår produktions miljö som vi behöver uppgradera?**

GA för den här funktionen är planerad innan MySQL v 5.6 upphör. Funktionen är dock produktions klar och stöds fullt ut av Azure, så du bör köra den med förtroende för din miljö. Som rekommenderad praxis rekommenderar vi starkt att du kör och testar det först på en återställd kopia av servern så att du kan beräkna stillestånds tiden under uppgraderingen och utföra test av programkompatibilitet innan du kör det på produktion. Mer information finns i [så här utför du](howto-restore-server-portal.md#point-in-time-restore) tidpunkts återställning för att skapa en tidpunkts kopia av servern. 

**2. kommer detta att orsaka drift stopp av servern och i så fall hur lång tid?**

Ja, servern kommer inte att vara tillgänglig under uppgraderings processen så vi rekommenderar att du utför den här åtgärden under det planerade underhålls fönstret. Den uppskattade stillestånds tiden beror på databasens storlek, den allokerade lagrings storleken (IOPs etablerad) och antalet tabeller i databasen. Uppgraderings tiden är direkt proportionell till antalet tabeller på servern. Uppgraderingar av Basic SKU-servrar förväntas ta längre tid eftersom de är på standard plattform för lagring. Vi rekommenderar att du först uppgraderar till en återställd kopia av servern för att beräkna stillestånds tiden för din server miljö.  

**3. det noteras att det inte stöds på replik servern ännu. Vad betyder det konkreta?**

För närvarande stöds inte högre versions uppgradering för replik servern, vilket innebär att du inte bör köra den för servrar som ingår i replikeringen (antingen käll-eller replik servern). Om du vill testa uppgraderingen av servrarna som ingår i replikeringen innan vi lägger till replik stödet för uppgraderings funktionen rekommenderar vi följande steg:

1. Under planerat underhåll stoppar du [replikeringen och tar bort replik servern](howto-read-replicas-portal.md) efter att du har samlat in dess namn och all konfigurations information (brand Väggs inställningar, Server parameter konfiguration om den skiljer sig från käll servern).
2. Utför uppgraderingen av käll servern.
3. Etablera en ny Läs replik server med samma namn och konfigurations inställningar som du hämtade i steg 1. Den nya replik servern kommer att finnas på v 5.7 automatiskt efter att käll servern har uppgraderats till v 5.7.

**4. Vad händer om vi inte väljer att uppgradera vår MySQL v 5.6-server före den 5 februari 2021?**

Du kan fortfarande fortsätta att köra MySQL v 5.6-servern som tidigare. Azure genomför **aldrig** Force-uppgraderingen på servern. De begränsningar som dokumenteras i [Azure Database for MySQL versions policy](concepts-version-policy.md) gäller dock.

## <a name="next-steps"></a>Nästa steg

Läs mer om [principer för Azure Database for MySQL versioner](concepts-version-policy.md).
