<properties
   pageTitle="SQL Azure med Azure RemoteApp | Microsoft Azure"
   description="Lär dig att använda SQL Azure med Azure RemoteApp."
   services="remoteapp"
   documentationCenter=""
   authors="ericorman"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="08/15/2016"
   ms.author="elizapo"/>

# SQL Azure med Azure RemoteApp

> [AZURE.IMPORTANT]
> Azure RemoteApp upphör att gälla. Läs [meddelandet](https://go.microsoft.com/fwlink/?linkid=821148) för mer information.

Ofta när kunder väljer att lagra sina Windows-program i molnet med Azure RemoteApp vill de också migrera data, till exempel SQL-servrar, till molnet för en hel molndistribution. Det gör att hela den molnbaserade lösningen kan användas när och var som helst av alla enheter, med Azure RemoteApp. Nedan visas länkar och referenser tillsammans med riktlinjer som hjälper dig med den här processen.  

## Migrera dina SQL-data

Börja med [Migrera en SQL Server-databas till Azure SQL Database](../sql-database/sql-database-cloud-migrate.md). 

## Konfigurera Azure RemoteApp
Använd Azure RemoteApp som värd för Windows-programmet. Nedan finns steg för steg-anvisningar på hög nivå:

1.     Skapa [VM-mallen för Azure RemoteApp](remoteapp-imageoptions.md). 
2.     Installera det program som krävs på den virtuella datorn (VM).
3.     Konfigurera programmet så att det ansluter till SQL DB och bekräfta att det fungerar.
4.     Kör en sysprep och stäng av den virtuella datorn. Samla in det som en avbildning för användning med Azure. **Obs!** Du måste kontrollera att programmet kan behålla DB-anslutningsinformationen under sysprep-processen. Om programmet inte kan behålla DB-anslutningsinformationen kan det vara bra att be programleverantören att kontrollera hur vi kan ange anslutningssträngen.
5.     Importera den anpassade avbildningen till Azure RemoteApp-biblioteket genom att välja den geografiska plats där din SQL Azure-distribution finns. 
6.     Distribuera en RemoteApp-samling i samma datacenter som din SQL Azure-distribution. Använd mallen ovan och publicera programmet. Genom att distribuera Azure RemoteApp i samma datacenter som SQL Azure får du de snabbaste anslutningshastigheterna och minskade svarstider. 

## Tänk på följande när du konfigurerar appen och SQL:
Det finns några saker att tänka på när du använder Azure SQL med RemoteApp:

Lär dig [hur du konfigurerar en Azure SQL Database-brandvägg](../sql-database/sql-database-firewall-configure.md). I ett utdrag ur artikeln står följande: ”Först blockeras all åtkomst till din Azure SQL Database-server av brandväggen. Innan du kan börja använda din Azure SQL Database-server måste du gå till den klassiska portalen och ange en eller flera brandväggsregler på servernivå som ger åtkomst till din Azure SQL Database-server. Använd brandväggsreglerna för att ange vilka IP-adressintervall från Internet som tillåts. Ange också om Azure-program kan försöka ansluta till din Azure SQL Database-server eller inte.”

Och när en dator försöker ansluta till databasservern från Internet, kontrollerar brandväggen den ursprungliga IP-adressen för begäran mot en fullständig uppsättning på servernivå och (vid behov) brandväggsregler på databasnivå . ”Om IP-adressen för begäran är inom ett intervall som anges i brandväggsreglerna på server-nivå, godkänns anslutningen till din Azure SQL Database-server." Därför kan vi utnyttja IP-intervall och inte bara enskilda IP-adresser.

Följ steg för steg-instruktionerna i [Gör så här: Konfigurera brandväggsinställningarna i SQL Database med Azure-portalen](../sql-database/sql-database-configure-firewall-settings.md) när du ska ange IP-intervall. När du konfigurerar SQL-brandväggsregler anger du IP-intervall för det undernät som har angetts för Azure RemoteApp-samling. Det gör att ARA-servrar ska kunna ansluta till SQL DB även om de har dynamiskt tilldelade IP-adresser.

## Felsökning
Det kan finnas flera skäl till att det verkar gå långsamt att använda ett klientprogram i Azure RemoteApp som ansluter till en SQL-databas som finns i Azure eller lokalt.  

- Nätverksfördröjningen från enheten till Azure är hög. Flytta till den bästa och snabbaste nätverksanslutningen du kan för bästa prestanda. Använd [azurespeed.com](http://azurespeed.com/) som ett allmänt verktyg för att testa enheternas fördröjning till Azure-datacenter.  
- Klientappen som finns i Azure RemoteApp är överbelastad. Öka prestandan genom att välja en annan faktureringsplan, till exempel Premiumfakturering. Ett annat tips är att övervaka de resurser som programmet förbrukar: Utför tangentsekvensen ctrl + alt + end under en aktiv session. Då öppnas SAS-skärmen där du väljer Aktivitetshanteraren och kan se resursanvändningen för din app.
- SQL-servern är överbelastad eller har inte optimerats. Följ SQL-riktlinjerna för felsökning. 




<!--HONumber=sep16_HO1-->


