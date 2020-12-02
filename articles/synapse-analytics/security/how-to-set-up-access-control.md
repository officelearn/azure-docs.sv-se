---
title: Skydda din Synapse-arbetsyta
description: I den här artikeln får du lära dig hur du använder roller och åtkomst kontroll för att styra aktiviteter och åtkomst till data i Synapse-arbetsytan.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: jrasnick
ms.openlocfilehash: 25e191af919c5880045a6c4c7c79b675cf02520e
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96458712"
---
# <a name="secure-your-synapse-workspace"></a>Skydda din Synapse-arbetsyta 

I den här artikeln får du lära dig hur du använder roller och åtkomst kontroll för att styra aktiviteter och åtkomst till data. Genom att följa dessa anvisningar är åtkomst kontroll i Azure Synapse Analytics förenklad. Du behöver bara lägga till och ta bort användare till en av tre säkerhets grupper.

## <a name="overview"></a>Översikt

För att skydda en Synapse-arbetsyta följer du ett mönster för att konfigurera följande objekt:

- Azure-roller (t. ex. inbyggda som ägare, deltagare osv.)
- Synapse-roller – dessa roller är unika för Synapse och baseras inte på Azure-roller. Det finns tre av följande roller:
  - Administratör för Synapse-arbetsyta
  - Synapse SQL-administratör
  - Apache Spark för Azure Synapse Analytics-administratör
- Åtkomst kontroll för data i Azure Data Lake Storage gen 2 (ADLSGEN2).
- Åtkomst kontroll för Synapse SQL-och Spark-databaser

## <a name="steps-to-secure-a-synapse-workspace"></a>Steg för att skydda en Synapse-arbetsyta

I det här dokumentet används standard namn för att förenkla anvisningarna. Ersätt dem med valfritt namn.

|Inställning | Exempelvärde | Description |
| :------ | :-------------- | :---------- |
| **Synapse-arbetsyta** | WS1 |  Namnet som Synapse-arbetsytan kommer att ha. |
| **ADLSGEN2-konto** | STG1 | ADLS-kontot som ska användas med din arbets yta. |
| **Container** | CNT1 | Den behållare i STG1 som arbets ytan ska använda som standard. |
| **Active Directory-klient** | contoso | Active Directory-klientens namn.|
||||

## <a name="step-1-set-up-security-groups"></a>STEG 1: Konfigurera säkerhets grupper

Skapa och fyll i tre säkerhets grupper för din arbets yta:

- **WS1 \_ WSAdmins** – för användare som behöver fullständig kontroll över arbets ytan
- **WS1 \_ SparkAdmins** – för de användare som behöver fullständig kontroll över Spark-aspekterna på arbets ytan
- **WS1 \_ SQLAdmins** – för användare som behöver fullständig kontroll över arbets ytans SQL-aspekter

## <a name="step-2-prepare-your-data-lake-storage-gen2-account"></a>STEG 2: Förbered ditt Data Lake Storage Gen2-konto

Identifiera den här informationen om din lagring:

- ADLSGEN2-kontot som ska användas för din arbets yta. Det här dokumentet anropar det STG1.  STG1 anses vara "primär" lagrings konto för din arbets yta.
- Behållaren i WS1 som din Synapse-arbetsyta ska använda som standard. Det här dokumentet anropar det CNT1.  Den här behållaren används för:
  - Lagra säkerhetskopieringsfilerna för Spark-tabeller
  - Körnings loggar för Spark-jobb

- Tilldela säkerhets grupperna följande roller på CNT1 med hjälp av Azure Portal

  - Tilldela **WS1 \_ WSAdmins** till rollen **Storage BLOB data Contributor**
  - Tilldela **WS1 \_ SparkAdmins** till rollen **Storage BLOB data Contributor**
  - Tilldela **WS1 \_ SQLAdmins** till rollen **Storage BLOB data Contributor**

## <a name="step-3-create-and-configure-your-synapse-workspace"></a>STEG 3: skapa och konfigurera din Synapse-arbetsyta

 I Azure Portal skapar du en arbets yta för Synapse:

- Välj din prenumeration
- Välj resurs grupp – du måste ha åtkomst till en resurs grupp som du har tilldelats **ägar** rollen för.
- Namnge arbets ytans WS1
- Välj STG1 för lagrings kontot –. Välj CNT1 för den behållare som används som "filesystem".
- Öppna WS1 i Synapse Studio
- Välj **Hantera**  >  **Access Control** tilldela säkerhets grupperna till följande Synapse-roller.
  - Tilldela **WS1- \_ WSAdmins** till Synapse arbets ytans administratörer
  - Tilldela **WS1 \_ SparkAdmins** till Synapse Spark-administratörer
  - Tilldela **WS1- \_ SQLAdmins** till Synapse SQL-administratörer

## <a name="step-4-configure-data-lake-storage-gen2-for-use-by-synapse-workspace"></a>STEG 4: Konfigurera Data Lake Storage Gen2 som ska användas av Synapse-arbetsytan

Synapse-arbetsytan behöver åtkomst till STG1 och CNT1 så att den kan köra pipeliner och utföra system uppgifter.

- Öppna Azure-portalen
- Hitta STG1
- Navigera till CNT1
- Se till att MSI (Hanterad tjänstidentitet) för WS1 har tilldelats rollen **Storage BLOB data Contributor** på CNT1
  - Om du inte ser den tilldelade tilldelar du den.
  - MSI har samma namn som arbets ytan. I det här fallet är det &quot; WS1 &quot; .

## <a name="step-5-configure-admin-access-for-synapse-sql"></a>STEG 5: Konfigurera administratörs åtkomst för Synapse SQL

- Öppna Azure-portalen
- Navigera till WS1
- Under **Inställningar** väljer du **SQL Active Directory admin**
- Välj **Ange administratör** och välj WS1 \_ SQLAdmins

## <a name="step-6-maintain-access-control"></a>STEG 6: Behåll åtkomst kontroll

Konfigurationen är färdig.

Nu kan du lägga till och ta bort användare i de tre säkerhets grupperna för att hantera åtkomst för användare.

Även om du kan tilldela användare till Synapse-roller manuellt, konfigureras det inte på ett konsekvent sätt. Lägg i stället till eller ta bort användare till säkerhets grupperna.

## <a name="step-7-verify-access-for-users-in-the-roles"></a>STEG 7: kontrol lera åtkomsten för användare i rollerna

Användare i varje roll behöver utföra följande steg:

| Antal | Steg | Arbetsytesadministratörer | Spark-administratörer | SQL-administratörer |
| --- | --- | --- | --- | --- |
| 1 | Ladda upp en Parquet-fil till CNT1 | JA | JA | JA |
| 2 | Läs filen Parquet med Server lös SQL-pool | JA | NO | JA |
| 3 | Skapa en server lös Apache Spark-pool | JA [1] | JA [1] | NO  |
| 4 | Läser filen Parquet med en bärbar dator | JA | JA | NO |
| 5 | Skapa en pipeline från antecknings boken och Utlös pipelinen för att köras nu | JA | NO | NO |
| 6 | Skapa en dedikerad SQL-pool och kör ett SQL-skript, till exempel &quot; Select 1&quot; | JA [1] | NO | JA [1] |

> [!NOTE]
> [1] om du vill skapa SQL-eller Spark-pooler måste användaren ha minst deltagar rollen i Synapse-arbetsytan.
>
 
>[!TIP]
> - Vissa steg kan inte avsiktligt tillåtas beroende på rollen.
> - Tänk på att vissa uppgifter kan Miss lyckas om inte säkerheten har kon figurer ATS fullständigt. Dessa uppgifter anges i tabellen.

## <a name="step-8-network-security"></a>STEG 8: nätverks säkerhet

Konfigurera arbets ytans brand vägg, virtuellt nätverk och [privat länk](../../azure-sql/database/private-endpoint-overview.md).

## <a name="step-9-completion"></a>STEG 9: Slutför

Din arbets yta är nu helt konfigurerad och säker.

## <a name="how-roles-interact-with-synapse-studio"></a>Hur roller interagerar med Synapse Studio

Synapse Studio fungerar olika beroende på användar roller. Vissa objekt kan vara dolda eller inaktiverade om en användare inte har tilldelats roller som ger lämplig åtkomst. I följande tabell sammanfattas effekterna på Synapse Studio.

| Uppgift | Arbets ytans administratörer | Spark-administratörer | SQL-administratörer |
| --- | --- | --- | --- |
| Öppna Synapse Studio | JA | JA | JA |
| Visa Home Hub | JA | JA | JA |
| Visa data hubb | JA | JA | JA |
| Data Hub/se länkade ADLS Gen2 konton och behållare | JA [1] | JA [1] | JA [1] |
| Data Hub/se databaser | JA | JA | JA |
| Data Hub/se objekt i databaser | JA | JA | JA |
| Data Hub/Access-data i Synapse SQL-databaser | JA   | NO   | JA   |
| Data Hub/Access-data i databaser med SQL-pooler utan Server | JA [2]  | NO  | JA [2]  |
| Data Hub/Access-data i Spark-databaser | JA [2] | JA [2] | JA [2] |
| Använd utveckla hubben | JA | JA | JA |
| Utveckla hubb/Author SQL-skript | JA | NO | JA |
| Utveckla jobb definitioner för hubb/Author Spark | JA | JA | NO |
| Utveckla hubbar/författar antecknings böcker | JA | JA | NO |
| Utveckla hubb/författar data flöden | JA | NO | NO |
| Använda Orchestration-hubben | JA | JA | JA |
| Dirigera hubb/Använd pipeliner | JA | NO | NO |
| Använd hantera hubb | JA | JA | JA |
| Hantera hubb/Synapse SQL | JA | NO | JA |
| Hantera hubb/Spark-pooler | JA | JA | NO |
| Hantera hubb/utlösare | JA | NO | NO |
| Hantera hubb/länkade tjänster | JA | JA | JA |
| Hantera hubb/Access Control (tilldela användare till Synapse-arbetsytans roller) | JA | NO | NO |
| Hantera nav/integrerings körningar | JA | JA | JA |
| Använd Monitor Hub | JA | JA | JA |
| Övervaka hubb/dirigering/pipeline-körningar  | JA | NO | NO |
| Övervaka hubb/dirigering/Utlös ande körningar  | JA | NO | NO |
| Övervaka hubb/Orchestration/integrerings körningar  | JA | JA | JA |
| Övervaka nav/aktiviteter/Spark-program | JA | JA | NO  |
| Övervaka hubb/aktiviteter/SQL-begäranden | JA | NO | JA |
| Övervaka hubb/aktiviteter/Spark-pooler | JA | JA | NO  |
| Övervaka hubb/utlösare | JA | NO | NO |
| Hantera hubb/länkade tjänster | JA | JA | JA |
| Hantera hubb/Access Control (tilldela användare till Synapse-arbetsytans roller) | JA | NO | NO |
| Hantera nav/integrerings körningar | JA | JA | JA |


> [!NOTE]
> [1] åtkomst till data i behållare beror på åtkomst kontrollen i ADLS Gen2. </br>
> [2] SQL-OD-tabeller och Spark-tabeller lagrar sina data i ADLS Gen2 och åtkomst kräver rätt behörighet för ADLS Gen2.

## <a name="next-steps"></a>Nästa steg

Skapa en [Synapse-arbetsyta](../quickstart-create-workspace.md)
