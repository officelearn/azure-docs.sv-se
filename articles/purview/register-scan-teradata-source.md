---
title: Registrera en Teradata-källa och installations genomsökningar (för hands version)
description: Den här artikeln beskriver hur du registrerar en Teradata-källa i Azure avdelningens kontroll och konfigurerar en genomsökning.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 12/06/2020
ms.openlocfilehash: 170ece293f8d24f3a29774c64c14f9334fe0930c
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96841954"
---
# <a name="register-and-scan-teradata-source-preview"></a>Registrera och Genomsök Teradata-källa (för hands version)

Den här artikeln beskriver hur du registrerar en Teradata-källa i avdelningens kontroll och konfigurerar en genomsökning.

> [!IMPORTANT]
> Den här data källan är för närvarande en för hands version. Du kan prova det och ge oss feedback.

## <a name="supported-capabilities"></a>Funktioner som stöds

Teradata-källan stöder **fullständig sökning** för att extrahera metadata från en Teradata-databas och hämtar **härkomst** mellan data till gångar.

## <a name="prerequisites"></a>Förutsättningar

- Anslutningen har stöd för data lager som bara finns i ett lokalt nätverk, ett virtuellt Azure-nätverk eller ett Amazon-virtuellt privat moln. Därför måste du konfigurera en [integration runtime med egen värd](manage-integration-runtimes.md) för att ansluta till den.

- Kontrol lera att Java Runtime Environment (JRE) är installerat på den virtuella datorn där integration runtime med egen värd är installerat.
 
- Se till att "Visual C++ Redistributable 2012 uppdatering 4" är installerat på den lokala datorn för integration Runtime. Om du inte redan har installerat det kan du ladda ned det [här](https://www.microsoft.com/download/details.aspx?id=30679).

- Du måste installera en driv rutin som heter Teradata JDBC-drivrutin manuellt på den lokala virtuella datorn. Den körbara JAR-filen kan hämtas från [Teradata-webbplatsen](https://downloads.teradata.com/).

    > [!Note] 
    > Driv rutinen bör vara tillgänglig för alla konton på den virtuella datorn. Installera inte i ett användar konto.

- De Teradata-databas versioner som stöds är **12. x till 16. x**. Se till att ha Läs behörighet till den Teradata-källa som genomsöks.

### <a name="feature-flag"></a>Funktions flagga

Registrering och genomsökning av en Teradata-källa är tillgängligt bakom en funktions flagga. Lägg till följande i din URL: *? Feature. ext. DataSource =% 7b "Teradata": "true"% 7d* 

T. ex. fullständig URL [ https://web.purview.azure.com/?feature.ext.datasource=%7b "metadata": "true"% 7d](https://web.purview.azure.com/?feature.ext.datasource=%7b"teradata":"true"%7d)

## <a name="setting-up-authentication-for-a-scan"></a>Konfigurera autentisering för en sökning
Det enda sätt som stöds för att konfigurera autentisering för en Teradata-källa är **grundläggande databas** -autentisering

## <a name="register-a-teradata-source"></a>Registrera en Teradata-källa

Gör så här för att registrera en ny Teradata-källa i data katalogen:

1. Navigera till ditt avdelningens kontroll-konto
2. Välj **källor** i det vänstra navigerings fältet
3. Välj **register**
4. På **register källor** väljer du **Teradata**
5. Välj **Fortsätt**

På sidan **Registrera källor (Teradata)** gör du följande:

1. Ange ett **namn** som data källan ska visas med i katalogen.
2. Ange **värd** namnet för att ansluta till en Teradata-källa. Det kan också vara en IP-adress eller en fullständigt kvalificerad anslutnings sträng till servern.
3. Välj en samling eller skapa en ny (valfritt)
4. **Slutför** för att registrera data källan.

    :::image type="content" source="media/register-scan-teradata-source/register-sources.png" alt-text="alternativ för registrering av källor" border="true":::

## <a name="creating-and-running-a-scan"></a>Skapa och köra en sökning

Om du vill skapa och köra en ny genomsökning gör du följande:
1. Klicka på *integration runtime* i hanterings centret. Kontrol lera att en lokal integration Runtime har kon figurer ATS. Om den inte har kon figurer ATS använder du stegen [här](manage-integration-runtimes.md) för att skapa en lokal integration runtime för skanning på en lokal eller virtuell Azure-dator som har åtkomst till ditt lokala nätverk.

2. Navigera sedan **källor**

3. Välj den registrerade Teradata-källan.

4. Välj + ny skanning
 
5. Ange informationen nedan:

    - Namn: namnet på genomsökningen

    - Anslut via integration Runtime: Välj den konfigurerade integrerings körningen med egen värd

    - Autentiseringsmetod: databas-autentisering är det enda alternativ som stöds för tillfället. Detta väljs som standard

    - Användar namn: ett användar namn för att ansluta till databas servern. Det här användar namnet ska ha Läs behörighet till servern

    - Lösen ord: användar lösen ordet som används för att ansluta till databas servern

    - Schema: lista över en delmängd av scheman som ska importeras uttrycks som en semikolonavgränsad lista. t. ex. Schema1; schema2. Alla användar scheman importeras om listan är tom. Alla system scheman (till exempel SysAdmin) och objekt ignoreras som standard.

        Acceptabla schema namn mönster med SQL LIKE-uttryck inkluderar att använda%, t. ex. en%; T % C%; Styr
        - börja med en eller    
        - sluta med B eller    
        - innehåller C eller    
        - lika med D

        Användning av icke-och specialtecken är inte acceptabelt

    - Driv rutins plats: fullständig sökväg till Teradata-drivrutinens plats på kundens virtuella dator. Driv rutins namnet för Teradata-JDBC måste vara: com. Teradata. JDBC. TeraDriver

    - Maximalt tillgängligt minne: högsta mängd minne (i GB) som är tillgängligt på kundens virtuella dator som ska användas genom genomsökning av processer. Detta beror på vilken storlek på Teradata-källan som ska genomsökas.

    > [!Note] 
    > Ange 2 GB minne för varje 1000 tabeller som en tumregel-regel

    :::image type="content" source="media/register-scan-teradata-source/setup-scan.png" alt-text="installations genomsökning" border="true":::

6. Klicka på *Fortsätt.*

7. Välj din genomsöknings utlösare. Du kan ställa in ett schema eller köra genomsökningen en gång.

    :::image type="content" source="media/register-scan-teradata-source/scan-trigger.png" alt-text="Genomsök utlösare" border="true":::

8. Granska din genomsökning och klicka på *Spara och kör.*

## <a name="viewing-your-scans-and-scan-runs"></a>Visa genomsökningar och skannings körningar

1. Gå till hanterings centret. Välj **data källor** under avsnittet **källor och sökning** .

2. Välj önskad data källa. En lista över befintliga genomsökningar visas på den data källan.

3. Välj den genomsökning vars resultat du är intresse rad av.

4. På den här sidan visas alla tidigare skannings körningar tillsammans med mått och status för varje genomsöknings körning. Den visar även om din genomsökning har schemalagts eller manuell, hur många till gångar som har klassificeringar tillämpade, hur många totala till gångar som har identifierats, start-och slut tid för genomsökningen och den totala genomsökningens varaktighet.

## <a name="manage-your-scans"></a>Hantera dina sökningar

Gör följande för att hantera eller ta bort en genomsökning:

1. Gå till hanterings centret. Välj **data källor** under avsnittet **källor och sökning** och välj sedan på önskad data källa.

2. Välj den genomsökning som du vill hantera. Du kan redigera sökningen genom att välja **Redigera**.

3. Du kan ta bort din genomsökning genom att välja **ta bort**.

## <a name="next-steps"></a>Nästa steg

- [Bläddra i Azure avdelningens kontroll Data Catalog](how-to-browse-catalog.md)
- [Sök i Azure avdelningens kontroll-Data Catalog](how-to-search-catalog.md)
