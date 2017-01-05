---
title: Kryptera en virtuell Azure-dator | Microsoft Docs
description: "Det här dokumentet hjälper dig att kryptera en virtuell Azure-dator när du har fått en avisering från Azure Security Center."
services: security, security-center
documentationcenter: na
author: TomShinder
manager: swadhwa
editor: 
ms.assetid: f6c28bc4-1f79-4352-89d0-03659b2fa2f5
ms.service: security
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/27/2016
ms.author: tomsh
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 83e277fe261338aed960dea8e2ab15cbff3c895e


---
# <a name="encrypt-an-azure-virtual-machine"></a>Kryptera en virtuell Azure-dator
Azure Security Center varnar dig om du har virtuella datorer som inte är krypterade. Dessa aviseringar visas med hög angelägenhetsgrad och rekommendationen är att kryptera dessa virtuella datorer.

![Rekommendation för kryptering av disk](./media/security-center-disk-encryption/security-center-disk-encryption-fig1.png)

> [!NOTE]
> Informationen i det här dokumentet rör förhandsversionen av Azure Security Center.
>
>

Vi rekommenderar följande steg när du ska kryptera virtuella Azure-datorer som har identifierats vara i behov av kryptering av Azure Security Center:

* Installera och konfigurera Azure PowerShell. Det gör att du kan köra de PowerShell-kommandon som krävs för att ställa in kraven och kryptera Azure-datorer.
* Hämta och kör de Azure PowerShell-skript som krävs för Azure Disk Encryption
* Kryptera dina virtuella datorer

Målet med det här dokumentet är att du ska kunna kryptera dina virtuella datorer, även om du har liten eller ingen erfarenhet av Azure PowerShell.
I det här dokumentet förutsätter vi att du använder Windows 10 som den klientdator där du ska konfigurera Azure Disk Encryption.

Det finns flera metoder som kan användas för att konfigurera de nödvändiga komponenterna och konfigurera kryptering för virtuella datorer i Azure. Om du redan känner till Azure PowerShell eller Azure CLI väl kanske du hellre vill använda alternativa metoder.

> [!NOTE]
> Mer info om alternativa sätt att konfigurera kryptering för virtuella Azure-datorer finns i [Azure Disk Encryption för Windows och virtuella Linux-datorer i Azure](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0).
>
>

## <a name="install-and-configure-azure-powershell"></a>Installera och konfigurera Azure PowerShell
Du behöver ha Azure PowerShell version 1.2.1 eller senare installerat på datorn. Artikeln [Så installerar och konfigurerar du Azure PowerShell](/powershell/azureps-cmdlets-docs) beskriver alla steg du behöver för att ställa in datorn så att den fungerar med Azure PowerShell. Den enklaste metoden är att använda metoden för Web PI-installation som beskrivs i den artikeln. Även om du redan har installerat Azure PowerShell bör du installera det igen med Web PI-metoden så att du har den senaste versionen av Azure PowerShell.

## <a name="obtain-and-run-the-azure-disk-encryption-prerequisites-configuration-script"></a>Hämta och kör det konfigurationsskript som krävs för Azure Disk Encryption
Med det nödvändiga konfigurationsskriptet för Azure Disk Encryption ställs alla förutsättningar som krävs för kryptering av dina virtuella Azure-datorer in.

1. Gå till den GitHub-sida som innehåller det [nödvändiga installationsskriptet för Azure Disk Encryption](https://github.com/Azure/azure-powershell/blob/dev/src/ResourceManager/Compute/Commands.Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1).
2. Klicka på **Raw**-knappen som finns på GibHub-sidan.
3. Markera all text på sidan genom att använda **CTRL-A**. Använd sedan **CTRL-C** och kopiera all text till Urklipp.
4. Öppna **Anteckningar** och klistra in den kopierade texten.
5. Skapa en ny mapp på enhet C: med namnet **AzureADEScript**.
6. Spara anteckningsfilen: Klicka på **Arkiv** och sedan på **Spara som**. I textrutan för filnamn anger du **”ADEPrereqScript.ps1”** och klickar på **Spara**. (Kontrollera att du placerar citattecknen runt namnet, annars sparas filen med filnamnstillägget .txt).

Nu när skriptinnehållet har sparats öppnar du skriptet i PowerShell ISE:

1. Klicka på **Cortana** i Start-menyn. Fråga **Cortana** ”PowerShell” genom att skriva **PowerShell** i Cortana-sökrutan.
2. Högerklicka på **Windows PowerShell ISE** och klicka på **Kör som administratör**.
3. I fönstret **Administratör: Windows PowerShell ISE** klickar du på **Visa** och sedan på **Visa skriptfönster**.
4. Om du ser fönstret **Kommandon** till höger klickar du på **”x”** i det övre högra hörnet för att stänga det. Om texten är för liten för att se använder du **CTRL + Lägg till** (”Lägg till” är tecknet ”+”). Om texten är för stor använder du **CTRL + subtrahera** (subtrahera är tecknet ”-”).
5. Klicka på **Arkiv** och sedan på **Öppna**. Navigera till mappen **C:\AzureADEScript** och dubbelklicka på **ADEPrereqScript**.
6. Innehållet **ADEPrereqScript** bör nu visas i PowerShell ISE. Det är färgkodat för att du enkelt ska kunna se diverse komponenter, till exempel kommandon, parametrar och variabler.

Du bör nu se något som liknar bilden nedan.

![Fönstret PowerShell ISE](./media/security-center-disk-encryption/security-center-disk-encryption-fig2.png)

Det översta fönstret kallas ”skriptfönster” och det längst ned kallas ”konsolen”. Vi använder dessa termer senare i den här artikeln.

## <a name="run-the-azure-disk-encryption-prerequisites-powershell-command"></a>Kör de PowerShell-kommandon som krävs för Azure Disk Encryption
Skriptet med kraven för Azure Disk Encryption ber dig om följande information när du har startat skriptet:

* **Resursgruppens namn** – namnet på den resursgrupp som du vill placera nyckelvalvet i.  Om det inte redan finns en med samma namn skapas en ny resursgrupp med det namn som du anger. Om du redan har en resursgrupp som du vill använda i den här prenumerationen anger du namnet på den.
* **Nyckelvalvets namn** – namnet på det nyckelvalv där krypteringsnycklarna ska placeras. Om det inte redan finns ett nyckelvalv med samma namn skapas ett nytt nyckelvalv med det här namnet. Om du redan har ett nyckelvalv som du vill använda anger du namnet på det befintliga nyckelvalvet.
* **Plats** – platsen för nyckelvalvet. Kontrollera att nyckelvalvet och virtuella datorer som ska krypteras finns på samma plats. Om du inte vet vilken plats det är finns steg senare i artikeln som visar hur du tar reda på det.
* **Azure Active Directory-programmets namn** – namnet på det Azure Active Directory-program som ska användas för att skriva hemligheter i nyckelvalvet. Om det inte redan finns ett program med det namnet skapas ett nytt. Om du redan har ett Azure Active Directory-program som du vill använda anger du namnet på det Azure Active Directory-programmet.

> [!NOTE]
> Om du är nyfiken på varför du behöver skapa ett Azure Active Directory-program kan du läsa avsnittet *Registrera ett program med Azure Active Directory* i artikeln [Komma igång med Azure-nyckelvalvet](../key-vault/key-vault-get-started.md).
>
>

Kryptera en virtuell Azure-dator genom att utföra följande steg:

1. Om du har stängt PowerShell ISE öppnar du en upphöjd PowerShell ISE-instans. Följ anvisningarna tidigare i den här artikeln om PowerShell ISE inte redan är öppet. Om du har avslutat skriptet öppnar du **ADEPrereqScript.ps1** genom att klicka på **Arkiv**. Klicka sedan på **Öppna** och välj skriptet i mappen **c:\AzureADEScript**. Om du har följt den här artikeln från början går du bara vidare till nästa steg.
2. I PowerShell ISE-konsolen (längst ned i PowerShell ISE) ändrar du fokus till det lokala skriptet genom att skriva **cd c:\AzureADEScript** och trycka på **RETUR**.
3. Ange körningsprincipen på datorn så att du kan köra skriptet. Skriv **Set-ExecutionPolicy Unrestricted** i konsolen och tryck på RETUR. Om du ser en dialogruta om effekterna av att ändra körningsprincipen klickar du antingen på **Ja till alla** eller **Ja** (om du ser **Ja till alla** markerar du det alternativet. Om du inte ser **Ja till alla** klickar du på **Ja**).
4. Logga in på Azure-kontot. Skriv **Login-AzureRmAccount** i konsolen och tryck på **RETUR**. En dialogruta visas där du anger dina autentiseringsuppgifter (kontrollera att du har behörighet att ändra de virtuella datorerna – om du inte har det kan du inte kryptera dem. Om du inte är säker frågar du din prenumerationsägare eller administratör). Du bör se information om din **miljö**, ditt **konto**, **klient-ID**, **prenumerations-ID** och **aktuellt lagringskonto**. Kopiera ditt **prenumerations-ID** till anteckningar. Du behöver använda det i steg 6.
5. Sök efter vilken prenumeration som den virtuella datorn tillhör och vilken plats den har. Gå till [https://portal.azure.com](ttps://portal.azure.com) och logga in.  Klicka på **virtuella datorer** till vänster. Du får se en lista över dina virtuella datorer och de prenumerationer som de tillhör.

   ![Virtuella datorer](./media/security-center-disk-encryption/security-center-disk-encryption-fig3.png)
6. Gå tillbaka till PowerShell ISE. Ställ in den prenumerationskontext som skriptet ska köras i. I konsolen skriver du **Select-AzureRmSubscription –SubscriptionId <your_subscription_Id>** (ersätt **< your_subscription_Id >** med ditt faktiska prenumerations-ID) och tryck på **RETUR**. Du ser information om din miljö, ditt **konto**, **klient-ID**, **prenumerations-ID** och **aktuellt lagringskonto**.
7. Du är nu redo att köra skriptet. Klicka på knappen **Kör skript** eller tryck på **F5** på tangentbordet.

   ![Köra PowerShell-skript](./media/security-center-disk-encryption/security-center-disk-encryption-fig4.png)
8. Skriptet begär **resourceGroupName:** – ange namnet på den *resursgrupp* du vill använda och tryck sedan på **RETUR**. Ange ett namn som du vill använda för en ny resursgrupp om du inte har en. Om du redan har en *resursgrupp* som du vill använda (till exempel den som den virtuella datorn är i) anger du namnet på den befintliga resursgruppen.
9. Skriptet begär **KeyVaultName:** – Ange namnet på det *nyckelvalv* du vill använda och tryck sedan på RETUR. Ange ett namn som du vill använda för ett nytt nyckelvalv om du inte har ett. Om du redan har ett nyckelvalv som du vill använda anger du namnet på det befintliga *nyckelvalvet*.
10. Skriptet begär **plats:** – Ange namnet på den plats där den virtuella datorn som du vill kryptera finns och tryck sedan på **RETUR**. Om du inte kommer ihåg platsen går du tillbaka till steg 5.
11. Skriptet begär **aadAppName:** – Ange namnet på det *Azure Active Directory*-program du vill använda och tryck sedan på **RETUR**. Ange ett namn som du vill använda för ett nytt program om du inte har ett. Om du redan har ett *Azure Active Directory-program*  som du vill använda anger du namnet på det befintliga *Azure Active Directory-programmet*.
12. En logg i dialogrutan visas. Ange dina autentiseringsuppgifter (ja, du har loggat in en gång, men nu måste du göra det igen).
13. Skriptet körs och när det är klart uppmanas du att kopiera värdena i **aadClientID**, **aadClientSecret**, **diskEncryptionKeyVaultUrl**, och **keyVaultResourceId**. Kopiera vart och ett av dessa värden till Urklipp och klistra in dem i anteckningar.
14. Gå tillbaka till PowerShell ISE och placera markören i slutet av den sista raden. Tryck på **RETUR**.

Skriptets utdata ska se ut ungefär som skärmen nedan:

![PowerShell-utdata](./media/security-center-disk-encryption/security-center-disk-encryption-fig5.png)

## <a name="encrypt-the-azure-virtual-machine"></a>Kryptera den virtuella Azure-datorn
Du är nu redo att kryptera din virtuella dator. Om den virtuella datorn finns i samma resursgrupp som ditt nyckelvalv kan du gå vidare till avsnittet med krypteringsstegen. Men om den virtuella datorn inte är i samma resursgrupp som ditt nyckelvalv måste du ange följande i konsolen i PowerShell ISE:

**$resourceGroupName = <’Virtual_Machine_RG’>**

Ersätt **< Virtual_Machine_RG >** med namnet på resursgruppen där de virtuella datorerna finns. Det ska omges av enkla citattecken. Tryck sedan på **RETUR**.
Bekräfta att rätt resursgruppsnamn har angetts genom att skriva följande i konsolen PowerShell ISE:

**$resourceGroupName**

Tryck på **RETUR**. Du bör se namnet på resursgruppen som de virtuella datorerna finns i. Några exempel:

![PowerShell-utdata](./media/security-center-disk-encryption/security-center-disk-encryption-fig6.png)

### <a name="encryption-steps"></a>Krypteringssteg
Först måste du ange namnet på den virtuella datorn som du vill kryptera för PowerShell. Skriv följande i konsolen:

**$vmName = <’your_vm_name’>**

Ersätt **< your_vm_name >** med namnet på den virtuella datorn (kontrollera att namnet omges av enkla citattecken) och tryck sedan på **RETUR**.

Bekräfta att rätt namn har angetts för den virtuella datorn genom att skriva:

**$vmName**

Tryck på **RETUR**. Du bör se namnet på den virtuella datorn som du vill kryptera. Några exempel:

![PowerShell-utdata](./media/security-center-disk-encryption/security-center-disk-encryption-fig7.png)

Det finns två sätt som du kan använda för att köra krypteringskommandot för den virtuella datorn. Den första metoden är att skriva följande kommando i konsolen PowerShell ISE:

~~~
Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $resourceGroupName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId
~~~

När du har skrivit det här kommandot trycker du på **RETUR**.

Den andra metoden är att klicka i skriptfönstret (den översta rutan i PowerShell ISE) och rulla längst ned i skriptet. Markera det kommando som anges ovan och högerklicka på det. Klicka sedan på **Kör markerat** eller tryck på **F8** på tangentbordet.

![PowerShell ISE](./media/security-center-disk-encryption/security-center-disk-encryption-fig8.png)

Oavsett vilken metod du använder visas en dialogruta som talar om att det tar 10–15 minuter att slutföra åtgärden. Klicka på **Ja**.

Medan krypteringsprocessen sker kan du gå tillbaka till Azure Portal och se status för den virtuella datorn. Klicka på **Virtuella datorer** till vänster. I bladet **Virtuella datorer** klickar du på namnet på den virtuella datorn som du krypterar. I bladet som visas kan du se att **Status** säger att den **uppdaterar**. Det visar att kryptering pågår.

![Mer information om den virtuella datorn](./media/security-center-disk-encryption/security-center-disk-encryption-fig9.png)

Gå tillbaka till PowerShell ISE. När skriptet har slutförts ser du det som visas i bilden nedan.

![PowerShell-utdata](./media/security-center-disk-encryption/security-center-disk-encryption-fig10.png)

Om du vill demonstrera att den virtuella datorn är krypterad går du tillbaka till Azure Portal och klickar på **Virtuella datorer** till vänster på sidan. Klicka på namnet på den virtuella datorn som du har krypterat. I bladet **Inställningar** klickar du på **Diskar**.

![Inställningsalternativ](./media/security-center-disk-encryption/security-center-disk-encryption-fig11.png)

I bladet **Diskar** kan du se att **Kryptering** är **aktiverat**.

![Diskegenskaper](./media/security-center-disk-encryption/security-center-disk-encryption-fig12.png)

## <a name="next-steps"></a>Nästa steg
I det här dokumentet beskrivs hur du krypterar en virtuell dator i Azure. I följande avsnitt kan du lära dig mer om Azure Security Center:

* [Säkerhetshälsoövervakning i Azure Security Center](security-center-monitoring.md)  – Här kan du läsa om hur du övervakar dina Azure-resursers hälsa
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md): Här finns vanliga frågor om tjänsten.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/): Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.



<!--HONumber=Dec16_HO1-->


