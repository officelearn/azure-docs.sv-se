---
title: Skydda din Synapse-arbetsyta (förhandsgranskning)
description: Den här artikeln lär dig hur du använder roller och åtkomstkontroll för att styra aktiviteter och åtkomst till data i Synapse-arbetsytan.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: jrasnick
ms.openlocfilehash: 94699f2153fa8d1df3ab85c184f32792c7ae0b59
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428880"
---
# <a name="secure-your-synapse-workspace-preview"></a>Skydda din Synapse-arbetsyta (förhandsgranskning)

Den här artikeln lär dig hur du använder roller och åtkomstkontroll för att styra aktiviteter och åtkomst till data. Genom att följa dessa instruktioner förenklas åtkomstkontrollen i Azure Synapse Analytics. Du behöver bara lägga till och ta bort användare i en av tre säkerhetsgrupper.

## <a name="overview"></a>Översikt

Om du vill skydda en Synaps-arbetsyta (förhandsgranskning) följer du ett mönster för att konfigurera följande objekt:

- Azure-roller (till exempel de inbyggda som Ägare, Deltagare, etc.)
- Synapse-roller – dessa roller är unika för Synapse och baseras inte på Azure-roller. Det finns tre av dessa roller:
  - Administrera synapsarbetsyta
  - Synapse SQL-administratör
  - Synapse Spark-admin
- Åtkomstkontroll för data i Azure Data Lake Storage Gen 2 (ADLSGEN2).
- Åtkomstkontroll för Synapse SQL- och Spark-databaser

## <a name="steps-to-secure-a-synapse-workspace"></a>Åtgärder för att skydda en Synapse-arbetsyta

I det här dokumentet används standardnamn för att förenkla instruktionerna. Ersätt dem med valfritt namn.

|Inställning | Exempelvärde | Beskrivning |
| :------ | :-------------- | :---------- |
| **Synapse arbetsyta** | WS1 (med) |  Namnet som Arbetsytan Synapse kommer att ha. |
| **ADLSGEN2-konto** | STG1 (AV ) | Adls-kontot som ska användas med arbetsytan. |
| **Container** | CNT1 (PÅ) | Behållaren i STG1 som arbetsytan ska använda som standard. |
| **Active Directory-klient** | contoso | active directory-klientnamnet.|
||||

## <a name="step-1-set-up-security-groups"></a>STEG 1: Konfigurera säkerhetsgrupper

Skapa och fyll i tre säkerhetsgrupper för din arbetsyta:

- **WS1\_WSAdmins** – för användare som behöver fullständig kontroll över arbetsytan
- **WS1\_SparkAdmins** – för de användare som behöver fullständig kontroll över Spark-aspekterna på arbetsytan
- **WS1\_SQLAdmins** – för användare som behöver fullständig kontroll över SQL-aspekterna på arbetsytan
- Lägg till **WS1-administratörer\_** i **WS1\_SQLAdmins**
- Lägg till **WS1-administratörer\_** i **WS1\_SparkAdmins**

## <a name="step-2-prepare-your-data-lake-storage-gen2-account"></a>STEG 2: Förbered ditt Data Lake Storage Gen2-konto

Identifiera den här informationen om ditt lagringsutrymme:

- ADLSGEN2-kontot som ska användas för arbetsytan. Detta dokument kallar det STG1.  STG1 anses vara det "primära" lagringskontot för din arbetsyta.
- Behållaren i WS1 som synapsarbetsytan ska använda som standard. Detta dokument kallar det CNT1.  Denna behållare används för:
  - Lagra bakgrundsdatafiler för Spark-tabeller
  - Körningsloggar för Spark-jobb

- Med hjälp av Azure-portalen tilldelar du säkerhetsgrupperna följande roller på CNT1

  - Tilldela **WS1\_WSAdmins** till rollen Storage **Blob Data Contributor**
  - Tilldela **WS1\_SparkAdmins** till rollen Storage **Blob Data Contributor**
  - Tilldela **WS1\_SQLAdmins** till rollen Storage **Blob Data Contributor**

## <a name="step-3-create-and-configure-your-synapse-workspace"></a>STEG 3: Skapa och konfigurera din Synapse-arbetsyta

Skapa en Synaps-arbetsyta i Azure-portalen:

- Namnge arbetsytan WS1
- Välj STG1 för lagringskontot
- Välj CNT1 för behållaren som används som "filsystem".
- Öppna WS1 i Synapse Studio
- Välj **Hantera** > **åtkomstkontroll** tilldela säkerhetsgrupperna till följande Synapse-roller.
  - Tilldela **WS1\_WSAdmins** till Synapse Workspace-administratörer
  - Tilldela **WS1\_SparkAdmins** till Synapse Spark-administratörer
  - Tilldela **WS1\_SQLAdmins** till Synapse SQL-administratörer

## <a name="step-4-configuring-data-lake-storage-gen2-for-use-by-synapse-workspace"></a>STEG 4: Konfigurera Data Lake Storage Gen2 för användning av Synapse-arbetsytan

Synapse-arbetsytan behöver åtkomst till STG1 och CNT1 så att den kan köra pipelines och utföra systemuppgifter.

- Öppna Azure-portalen
- Hitta STG1
- Navigera till CNT1
- Kontrollera att MSI för WS1 har tilldelats azure **blob-datadeltagare** roll på CNT1
  - Om du inte ser den tilldelad, tilldela den.
  - MSI har samma namn som arbetsytan. I det här fallet &quot;skulle&quot;det vara WS1 .

## <a name="step-5-configure-admin-access-for-sql-pools"></a>STEG 5: Konfigurera administratörsåtkomst för SQL-pooler

- Öppna Azure-portalen
- Navigera till WS1
- Klicka på **SQL Active Directory-administratör under** **Inställningar**
- Klicka på **Ange administratör** \_och välj WS1 SQLAdmins

## <a name="step-6-maintaining-access-control"></a>STEG 6: Upprätthålla åtkomstkontroll

Konfigurationen är klar.

Nu, för att hantera åtkomst för användare, kan du lägga till och ta bort användare till de tre säkerhetsgrupperna.

Även om du manuellt kan tilldela användare till Synapse-roller, konfigurerar den inte saker konsekvent om du gör det. Lägg i stället bara till eller ta bort användare i säkerhetsgrupperna.

## <a name="step-7-verify-access-for-users-in-the-roles"></a>STEG 7: Verifiera åtkomst för användare i rollerna

Användare i varje roll måste utföra följande steg:

|   | Steg | Arbetsytesadministratörer | Spark-administratörer | SQL-administratörer |
| --- | --- | --- | --- | --- |
| 1 | Ladda upp en parkettfil till CNT1 | JA | JA | JA |
| 2 | Läs parkettfilen med SQL på begäran | JA | NO | JA |
| 3 | Skapa en Spark-pool | JA [1] | JA [1] | NO  |
| 4 | Läser parkettfilen med en anteckningsbok | JA | JA | NO |
| 5 | Skapa en pipeline från anteckningsboken och utlösa pipelinen som ska köras nu | JA | NO | NO |
| 6 | Skapa en SQL Pool och kör &quot;ett SQL-skript som SELECT 1&quot; | JA [1] | NO | JA[1] |

> [!NOTE]
> [1] För att skapa SQL- eller Spark-pooler måste användaren ha minst deltagarrollen på Synapse-arbetsytan.
> [!TIP]
>
> - Vissa steg kommer medvetet inte att tillåtas beroende på vilken roll.
> - Tänk på att vissa aktiviteter kan misslyckas om säkerheten inte har konfigurerats helt. Dessa uppgifter noteras i tabellen.

## <a name="step-8-network-security"></a>STEG 8: Nätverkssäkerhet

Så här konfigurerar du arbetsytebrandväggen, det virtuella nätverket och [den privata länken](../../sql-database/sql-database-private-endpoint-overview.md).

## <a name="step-9-completion"></a>STEG 9: Slutförande

Arbetsytan är nu helt konfigurerad och säkrad.

## <a name="how-roles-interact-with-synapse-studio"></a>Hur roller interagerar med Synapse Studio

Synapse Studio kommer att bete sig annorlunda baserat på användarroller. Vissa objekt kan vara dolda eller inaktiverade om en användare inte tilldelas roller som ger rätt åtkomst. I följande tabell sammanfattas effekten på Synapse Studio.

| Aktivitet | Administratörer på arbetsyta | Spark-administratörer | SQL-administratörer |
| --- | --- | --- | --- |
| Öppna Synapse Studio | JA | JA | JA |
| Visa starthubben | JA | JA | JA |
| Visa datahubben | JA | JA | JA |
| Data Hub / Se länkade ADLSGen2-konton och behållare | JA [1] | JA[1] | JA[1] |
| Datahubben / Se databaser | JA | JA | JA |
| Datahubben / Se objekt i databaser | JA | JA | JA |
| DataHub / Access-data i SQL pool databaser | JA   | NO   | JA   |
| DataHub / Access-data i SQL on-demand-databaser | JA [2]  | NO  | JA [2]  |
| DataHub / Access-data i Spark-databaser | JA [2] | JA [2] | JA [2] |
| Använd hubben Utveckla | JA | JA | JA |
| Utveckla Hub / författare SQL Scripts | JA | NO | JA |
| Utveckla Hub / författare Spark Job Definitioner | JA | JA | NO |
| Utveckla hubb-/författaredatorböcker | JA | JA | NO |
| Utveckla dataflöden för hubb/författare | JA | NO | NO |
| Använda orchestrate-hubben | JA | JA | JA |
| Orchestrate hub / använd Pipelines | JA | NO | NO |
| Använda hantera hubben | JA | JA | JA |
| Hantera Hub / SQL-pooler | JA | NO | JA |
| Hantera hubb- och sparkpooler | JA | JA | NO |
| Hantera hubb/utlösare | JA | NO | NO |
| Hantera hubb-/länkade tjänster | JA | JA | JA |
| Hantera hubb-/åtkomstkontroll (tilldela användare till Synapse-arbetsyteroller) | JA | NO | NO |
| Hantera hubb-/integrationskörningar | JA | JA | JA |

> [!NOTE]
> [1] Åtkomst till data i behållare beror på åtkomstkontrollen i ADLSGen2 [2] SQL OD-tabeller och Spark-tabeller lagrar sina data i ADLSGen2 och åtkomst kräver lämpliga behörigheter för ADLSGen2.

## <a name="next-steps"></a>Nästa steg

Skapa en [Synapse-arbetsyta](../quickstart-create-workspace.md)
