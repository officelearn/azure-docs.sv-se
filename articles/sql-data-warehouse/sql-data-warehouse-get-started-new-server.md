<properties
   pageTitle="Skapa en SQL Data Warehouse-databas i Azure Portal | Microsoft Azure"
   description="Lär dig hur du skapar ett Azure SQL Data Warehouse i Azure Portal"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="jhubbard"
   editor=""
   tags="azure-sql-data-warehouse"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/03/2016"
   ms.author="lodipalm;"/>

# Skapa en ny logisk server

I SQL Database och SQL Data Warehouse är varje databas tilldelad till en server och varje server är tilldelad till en geografisk plats. Servern kallas för en logisk SQL-server.

> [AZURE.NOTE] <a name="note"></a>En logisk SQL-server:
  >
  > + Ger ett konsekvent sätt att konfigurera flera databaser inom samma geografiska plats.
  > + Är inte fysisk maskinvara som för en lokal server. Är en del av tjänstprogramvaran. Det är därför den kallas en *logisk server*.
  > + Kan vara värd åt flera databaser utan att påverka deras prestanda.
  > + Använder ett litet *s* i sitt namn. SQL-**s**erver är en logisk Azure-server medan SQL **S**erver är Microsofts lokala databasprodukt.

1. Klicka på **Server** > **Skapa en ny server**. Servern är kostnadsfri. Om du redan har en logisk V12 SQL-server som du vill använda, väljer du din befintliga server och går till nästa steg.

    ![Skapa en ny server](./media/sql-data-warehouse-get-started-provision/create-server.png)

2. Fyll i **Ny server**-informationen.

    - **Servernamn**: Ange ett namn för din logiska server. Detta är unik för varje geografisk plats.
    - **Namn på serveradministratör**: Ange ett användarnamn för serveradministratörs-kontot.
    - **Lösenordet**: Ange lösenord för serveradministratören.
    - **Plats**: Välj en geografisk plats för din server. För att minska dataöverföringstid så är det bäst att placera din server geografiskt nära andra dataresurser som databasen kommer att använda sig av.
    - **Skapa V12-Server**: JA är det enda alternativet för SQL Data Warehouse.
    - **Ge Azure-tjänster åtkomst till servern**: Det här är alltid markerat för SQL Data Warehouse

    >[AZURE.NOTE] Var noga med att lagra servernamnet samt namn och lösenord för serveradministratören någonstans.  Du kommer att behöva den informationen för att logga in på servern.

3. Klicka på **OK** för att spara inställningarna för den logiska SQL-serverkonfigurationen och återgå till SQL Data Warehouse-bladet.

    ![Konfigurera en ny server](./media/sql-data-warehouse-get-started-provision/configure-server.png)



<!--HONumber=Jun16_HO2-->


