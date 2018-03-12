---
title: Ansluta en Amazon Web Services-konto till Azure kostnaden Management | Microsoft Docs
description: "Ansluta en Amazon Web Services-konto om du vill visa kostnad och användningsdata i kostnad rapporter."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 02/08/2018
ms.topic: article
ms.service: cost-management
manager: carmonm
ms.custom: 
ms.openlocfilehash: 4a0280420132aad9f1e0b17d5998ec225bb0eaa1
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2018
---
# <a name="connect-an-amazon-web-services-account"></a>Ansluta en Amazon Web Services-konto

Du har två alternativ för att ansluta ditt konto Amazon Web Services (AWS) till Azure kostnaden Management. Du kan ansluta med en IAM-roll eller med ett skrivskyddat användarkonto för IAM. Rollen IAM rekommenderas eftersom du kan delegera åtkomst med definierade behörighet till betrodda enheter. Rollen IAM behöver du inte dela långsiktiga åtkomstnycklar. När du ansluter ett AWS-konto till kostnaden Management är kostnad och användningsdata tillgängliga i kostnaden Management rapporter. Det här dokumentet hjälper dig att båda alternativen.

Mer information om AWS IAM identiteter finns [identiteter (användare, grupper och roller)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id.html).

## <a name="aws-role-based-access"></a>AWS rollbaserad åtkomst

Följande avsnitt beskriver hur du skapar en skrivskyddad IAM-roll för att ge åtkomst till hantering av kostnaden.

### <a name="get-your-cost-management-account-external-id"></a>Hämta kostnaden Management konto externa-ID

Det första steget är att hämta lösenfrasen unik anslutning från hanteringsportalen för Azure kostnaden. Den används i AWS som den **externa ID**.

1. Öppna Cloudyn portalen från Azure-portalen eller navigera till [https://azure.cloudyn.com](https://azure.cloudyn.com) och logga in.
2. Klicka på **inställningar** (kugghjulet ikonen) och välj sedan **moln konton**.
3. I hantering av konton, väljer du den **AWS konton** och klicka sedan på **Lägg till ny +**.
4. I den **Lägg till konto AWS** dialogrutan, kopiera den **externa ID** och spara det här värdet för AWS rollen skapa stegen i nästa avsnitt. I följande bild, exempel-ID: T är _Cloudyn_. Ditt ID skiljer sig åt.  
    ![Externt ID](./media/connect-aws-account/external-id.png)

### <a name="add-aws-read-only-role-based-access"></a>Lägg till AWS skrivskyddad rollbaserad åtkomst

1. Logga in på AWS-konsolen på https://console.aws.amazon.com/iam/home och välj **roller**.
2. Klicka på **skapa roll** och välj sedan **en annan AWS konto**.
3. Klistra in identiteten `432263259397` i den **konto-ID** fältet. Detta konto-ID är kostnad data collector hanteringskontot som du måste använda.
4. Bredvid **alternativ**väljer **kräver externa ID** klistra in det värde som du kopierade tidigare i den **externa ID** fältet och klickar sedan på **nästa: Behörigheter**.  
    ![Skapa en roll](./media/connect-aws-account/create-role01.png)
5. Under **ansluta behörigheterna principer**i den **principtypen** filter rutan Sök, skriver `ReadOnlyAccess`väljer **ReadOnlyAccess**, klicka på **nästa: Granska**.  
    ![Skrivskyddad åtkomst](./media/connect-aws-account/readonlyaccess.png)
6. På sidan Granska Kontrollera inställningarna är korrekta och ange en **rollnamn**. Till exempel *Azure-kostnad-Mgt*. Ange en **Rollbeskrivning**. Till exempel _rolltilldelning för Azure kostnaden Management_, klicka på **skapa roll**.
7. I den **roller** listan, klicka på den roll som du har skapat och kopiera den **rollen ARN** värde från sammanfattningssidan. Använd värdet rollen ARN senare när du registrerar din konfiguration i Azure kostnaden Management.  
    ![Rollen ARN](./media/connect-aws-account/role-arn.png)

### <a name="configure-aws-iam-role-access-in-cost-management"></a>Konfigurera AWS IAM rollåtkomst i kostnaden Management

1. Öppna Cloudyn portalen från Azure-portalen eller navigera till https://azure.cloudyn.com/ och logga in.
2. Klicka på **inställningar** (kugghjulet ikonen) och välj sedan **moln konton**.
3. I hantering av konton, väljer du den **AWS konton** och klicka sedan på **Lägg till ny +**.
4. I **kontonamn**, Skriv ett namn för kontot.
5. Bredvid **behörighet**väljer **IAM rollen**.
6. I den **rollen ARN** fältet, klistra in det värde som du kopierade tidigare och klicka sedan på **spara**.  
    ![Status för AWS](./media/connect-aws-account/aws-account-status01.png)

AWS-konto visas i listan över konton. Den **ägar-ID** i listan matchar rollen ARN-värde. Din **kontostatus** ska ha en grön bock symbol.

Kostnad Management börjar samla in data och skapa rapporter. Vissa optimering rapporter kräva data från ett par dagar innan korrekt rekommendationer genereras.

## <a name="aws-user-based-access"></a>AWS användarbaserade åtkomst

Följande avsnitt beskriver hur du skapar en skrivskyddad användare för att ge åtkomst till hantering av kostnaden.

### <a name="add-aws-read-only-user-based-access"></a>Lägg till AWS användarbaserade läsbehörighet

1. Logga in på AWS-konsolen på https://console.aws.amazon.com/iam/home och välj **användare**.
2. Klicka på **lägga till användare**.
3. I den **användarnamn** skriver du ett användarnamn.
4. För **komma åt typen**väljer **Programmeringsåtkomst** och på **nästa: behörigheter**.  
    ![Lägg till användare](./media/connect-aws-account/add-user01.png)
5. Behörigheter, Välj **koppla befintliga principer direkt**.
6. Under **ansluta behörigheterna principer**i den **principtypen** filter rutan Sök, skriver `ReadOnlyAccess`väljer **ReadOnlyAccess**, och klicka sedan på **nästa : Granska**.  
    ![Ange behörigheter för användare](./media/connect-aws-account/set-permission-for-user.png)
7. Se till att inställningarna är korrekta och klicka sedan på sidan Granska **skapa användare**.
8. På sidan har slutförts visas din nyckel-ID och Hemlig åtkomst snabbtangent. Du kan använda den här informationen för att konfigurera registrering i hantering av kostnaden.
9. Klicka på **hämta .csv** och spara filen credentials.csv till en säker plats.  
    ![Hämta autentiseringsuppgifter](./media/connect-aws-account/download-csv.png)


### <a name="configure-aws-iam-user-based-access-in-cost-management"></a>Konfigurera AWS IAM användarbaserade åtkomst i kostnaden Management

1. Öppna Cloudyn portalen från Azure-portalen eller navigera till https://azure.cloudyn.com/ och logga in.
2. Klicka på **inställningar** (kugghjulet ikonen) och välj sedan **moln konton**.
3. I hantering av konton, väljer du den **AWS konton** och klicka sedan på **Lägg till ny +**.
4. För **kontonamn**, ange ett kontonamn.
5. Bredvid **behörighet**väljer **IAM användaren**.
6. I **åtkomstnyckeln**, klistra in den **åtkomst nyckeln ID** värdet från filen credentials.csv.
7. I **hemlig nyckel**, klistra in den **hemliga åtkomstnyckeln** värdet från credentials.csv-filen och klicka sedan på **spara**.  
    ![Konto för AWS användarstatus](./media/connect-aws-account/aws-user-account-status.png)

AWS-konto visas i listan över konton. Din **kontostatus** ska ha en grön bock symbol.

Kostnad Management börjar samla in data och skapa rapporter. Vissa optimering rapporter kräva data från ett par dagar innan korrekt rekommendationer genereras.

## <a name="next-steps"></a>Nästa steg

- Om du vill veta mer om Azure kostnaden Management kan fortsätta att den [granska användning och kostnader](tutorial-review-usage.md) självstudiekurs för hantering av kostnaden.
