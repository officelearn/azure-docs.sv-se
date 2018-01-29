---
title: "Precis i tid virtuella datorn åtkomst till i Azure Security Center | Microsoft Docs"
description: "Det här dokumentet visar hur precis i tid VM åtkomst i Azure Security Center hjälper dig att styra åtkomsten till din virtuella Azure-datorer."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/26/2018
ms.author: terrylan
ms.openlocfilehash: a15857f0df5c967031aed00d89e71b3199eed0c4
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2018
---
# <a name="manage-virtual-machine-access-using-just-in-time-preview"></a>Hantera virtuella åtkomst med hjälp av precis i tid (förhandsgranskning)

Precis i tid virtuell dator (VM) kan åtkomst användas för att låsa inkommande trafik till din virtuella Azure-datorer minskar risken för attacker och enkel åtkomst till att ansluta till virtuella datorer när de behövs.

> [!NOTE]
> Den bara i tiden funktionen är i förhandsvisning och finns på standardnivån av Security Center.  Se [priser](security-center-pricing.md) att lära dig mer om Security Center prisnivåer.
>
>

## <a name="attack-scenario"></a>Angrepp

Brute force-attacker ofta målportar för hantering som ett sätt att få åtkomst till en virtuell dator. Om det lyckas, kan en angripare ta kontroll över den virtuella datorn och upprätta en fot i din miljö.

Ett sätt att minska risken för ett nyckelsökningsangrepp är att begränsa den tid som en port är öppen. Hanteringsportar behöver inte vara öppen när som helst. De behöver bara vara öppen när du är ansluten till den virtuella datorn, till exempel för att utföra uppgifter för hantering och underhåll. När precis i tid är aktiverat, Security Center använder [Nätverkssäkerhetsgruppen](../virtual-network/virtual-networks-nsg.md) (NSG) regler som begränsar åtkomsten till hanteringsportar och de inte kan riktas av angripare.

![Precis i tid scenario][1]

## <a name="how-does-just-in-time-access-work"></a>Hur just-in-time-åtkomst fungerar?

När JIT (Just-In-Time) är aktiverat begränsar Security Center inkommande trafik till dina virtuella Azure-datorer genom att skapa en NSG-regel. Du väljer portar på den virtuella datorn som inkommande trafik ska låsas. Dessa portar som styrs av den bara i Tidslösning.

När en användare begär åtkomst till en virtuell dator, Security Center kontrollerar att användaren har [rollbaserad åtkomstkontroll (RBAC)](../active-directory/role-based-access-control-configure.md) behörigheter som ger skrivbehörighet för den virtuella datorn. Om de har behörighet att skriva begäran har godkänts och Security Center automatiskt konfigurerar Nätverkssäkerhetsgrupper (NSG: er) för att tillåta inkommande trafik till hanteringsportar för den tid angett du. När tid har gått ut, återställer Security Center de NSG: er till sitt tidigare tillstånd.

> [!NOTE]
> Security Center just-in-time-åtkomst för VM stöder för närvarande endast virtuella datorer som distribueras via Azure Resource Manager. Mer information om klassiskt och Resource Manager distributionsmodellerna finns [Azure Resource Manager och klassisk distribution](../azure-resource-manager/resource-manager-deployment-model.md).
>
>

## <a name="using-just-in-time-access"></a>Med hjälp av just-in-time-åtkomst

1. Öppna instrumentpanelen för **Security Center**.

2. I den vänstra rutan, Välj **precis i tid VM access**.

![Precis i tid VM åtkomst panelen][2]

Den **precis i tid VM access** öppnas.

![Precis i tid VM åtkomst panelen][10]

**Just-in-time-åtkomst för VM** innehåller information om tillståndet för dina virtuella datorer:

- **Konfigurerad** -virtuella datorer som har konfigurerats för att stödja just-in-time VM-åtkomst. Data som visas för den senaste veckan och innehåller antalet godkända begäranden, datumet och tiden och senast användare för varje virtuell dator.
- **Rekommenderade** -virtuella datorer som stöder just-in-time-åtkomst för VM men inte har konfigurerats att. Vi rekommenderar att du aktiverar precis i tid VM åtkomstkontroll för dessa virtuella datorer. Se [konfigurerar bara i tid åtkomstprincip](#configuring-a-just-in-time-access-policy).
- **Ingen rekommendation** -orsaker till att en virtuell dator inte till rekommenderas är:
  - NSG - på samma sätt som saknas i tid lösning kräver en NSG ska vara på plats.
  - Klassiska VM - Security Center just-in-time-åtkomst för VM stöder för närvarande endast virtuella datorer som distribueras via Azure Resource Manager. Klassisk distribution stöds inte av den bara i Tidslösning.
  - Andra - en virtuell dator finns i den här kategorin om den bara i tid lösningen har inaktiverats i säkerhetsprincipen för prenumerationen eller resursgruppen och att den virtuella datorn saknar en offentlig IP-adress och inte har en NSG på plats.

## <a name="configuring-a-just-in-time-access-policy"></a>Konfigurera bara i tid åtkomstprincip

Så här väljer du de virtuella datorer som du vill aktivera:

1. Under **precis i tid VM access**, Välj den **rekommenderas** fliken.

  ![Aktivera just-in-time-åtkomst][3]

2. Under **VIRTUELLA**, Välj de virtuella datorer som du vill aktivera. Detta placerar en bock bredvid en virtuell dator.
3. Välj **aktivera JIT på virtuella datorer**.
4. Välj **Spara**.

### <a name="default-ports"></a>Standardportar

Du kan se standardportarna som Security Center rekommenderar att aktivera precis i tid.

1. Under **precis i tid VM access**, Välj den **rekommenderas** fliken.

  ![Visa standardportarna][6]

2. Under **VMs**, Välj en virtuell dator. Detta placerar en bock bredvid den virtuella datorn och öppnar **JIT VM konfiguration**. Det här bladet visar standardportarna.

### <a name="add-ports"></a>Lägga till portar

Under **JIT VM konfiguration**, du kan också lägga till och konfigurera en ny port som du vill aktivera den bara i Tidslösning.

1. Under **JIT VM konfiguration**väljer **Lägg till**. Då öppnas **Lägg till Portkonfiguration**.

  ![Portkonfiguration][7]

2. Under **Lägg till Portkonfiguration**, du identifierar porten, protokolltyp, tillåtna käll-IP-adresser och tid för maximal begäran.

  Tillåtna käll-IP-adresser är IP-adressintervall som tillåts att få åtkomst vid ett godkänt förfrågan.

  Tid för maximal begäran är fönstret maximal tid som en specifik port kan öppnas.

3. Välj **OK**.

## <a name="requesting-access-to-a-vm"></a>Begär åtkomst till en virtuell dator

Att begära åtkomst till en virtuell dator:

1. Under **precis i tid VM access**, Välj den **konfigurerad** fliken.
2. Under **VMs**, Välj de virtuella datorerna som du vill aktivera åtkomst. Detta placerar en bock bredvid en virtuell dator.
3. Välj **begära åtkomst**. Då öppnas **begära åtkomst**.

  ![Begär åtkomst till en virtuell dator][4]

4. Under **begära åtkomst**, konfigurera för varje virtuell portar öppna tillsammans med som porten är öppen på käll-IP och tidsfönstret porten är öppen. Du kan begära åtkomst till de portar som konfigurerats i den bara i tid princip. Varje port har en högsta tillåtna tid som härletts från den bara i tid princip.
5. Välj **öppna portarna**.

## <a name="editing-a-just-in-time-access-policy"></a>Redigera bara i tid åtkomstprincip

Du kan ändra en virtuell dator finns bara i tid princip genom att lägga till och konfigurera en ny port ska öppnas för den virtuella datorn eller genom att ändra andra parametrar som är relaterade till en port som redan skyddad.

För att redigera en befintlig precis i tid princip för en virtuell dator i **konfigurerad** används:

1. Under **VMs**, Välj en virtuell dator att lägga till en port för genom att klicka på de tre punkterna i raden för den virtuella datorn. Då öppnas en meny.
2. Välj **redigera** på menyn. Då öppnas **JIT VM konfiguration**.

  ![Redigera princip][8]

3. Under **JIT VM konfiguration**, du kan redigera de befintliga inställningarna för en redan skyddade port antingen genom att klicka på dess port och du kan välja **Lägg till**. Då öppnas **Lägg till Portkonfiguration**.

  ![Lägga till en port][7]

4. Under **Lägg till Portkonfiguration**, identifierar porten, protokolltyp tillåtna IP-adresser för källa och maximal tid för begäran.
5. Välj **OK**.
6. Välj **Spara**.

## <a name="auditing-just-in-time-access-activity"></a>Granskning precis i tid access-aktivitet

Du kan få insikter om VM aktiviteter loggen sökning. Visa loggar:

1. Under **precis i tid VM access**, Välj den **konfigurerad** fliken.
2. Under **VMs**, Välj en virtuell dator för att visa information om genom att klicka på de tre punkterna i raden för den virtuella datorn. Då öppnas en meny.
3. Välj **aktivitetsloggen** på menyn. Då öppnas **aktivitetsloggen**.

  ![Välj aktivitetsloggen][9]

  **Aktivitetsloggen** en filtrerad vy över tidigare åtgärder för den virtuella datorn tillsammans med tid och datum prenumeration.

  ![Visa aktivitetsloggen][5]

Du kan hämta logginformation genom att välja **Klicka här för att hämta alla objekt som CSV**.

Ändra filter och välj **tillämpa** att skapa en sökning och logg.

## <a name="using-just-in-time-vm-access-via-powershell"></a>Med hjälp av precis i tid VM åtkomst via PowerShell

För att kunna använda den bara i Tidslösning via PowerShell kan du kontrollera att den [senaste](/powershell/azure/install-azurerm-ps) Azure PowerShell-version.
När du gör det, måste du installera den [senaste](https://aka.ms/asc-psgallery) Azure Security Center från PowerShell-galleriet.

### <a name="configuring-a-just-in-time-policy-for-a-vm"></a>Konfigurera bara i tid princip för en virtuell dator

Så här konfigurerar du bara i tid princip på en specifik VM, måste du köra det här kommandot i PowerShell-sessionen: Set-ASCJITAccessPolicy.
Följ cmdlet-dokumentationen om du vill veta mer.

### <a name="requesting-access-to-a-vm"></a>Begär åtkomst till en virtuell dator

Åtkomst till en specifik VM som skyddas med den bara i tid lösningen måste du köra det här kommandot i PowerShell-sessionen: anropa ASCJITAccess.
Följ cmdlet-dokumentationen om du vill veta mer.

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig hur precis i tid VM åtkomst i Security Center hjälper du styr åtkomst till din Azure virtuella datorer.

I följande avsnitt kan du lära dig mer om Security Center:

- [Ställa in säkerhetsprinciper](security-center-policies.md) – Lär dig hur du ställer in säkerhetsprinciper för dina Azure-prenumerationer och resursgrupper.
- [Hantera säkerhetsrekommendationer](security-center-recommendations.md) – Lär dig rekommendationer för att skydda dina Azure-resurser.
- [Övervakning av säkerhetshälsa](security-center-monitoring.md) – Lär dig att övervaka hälsotillståndet hos dina Azure-resurser.
- [Hantera och åtgärda säkerhetsaviseringar](security-center-managing-and-responding-alerts.md) – Lär dig hur du hanterar och åtgärdar säkerhetsaviseringar.
- [Övervaka partnerlösningar](security-center-partner-solutions.md) – Lär dig hur du övervakar dina partnerlösningars hälsostatus.
- [Vanliga frågor om Security Center](security-center-faq.md) – finns vanliga frågor om hur du använder tjänsten.
- [Azures säkerhetsblogg](https://blogs.msdn.microsoft.com/azuresecurity/) – Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.


<!--Image references-->
[1]: ./media/security-center-just-in-time/just-in-time-scenario.png
[2]: ./media/security-center-just-in-time/just-in-time.png
[10]: ./media/security-center-just-in-time/just-in-time-access.png
[3]: ./media/security-center-just-in-time/enable-just-in-time-access.png
[4]: ./media/security-center-just-in-time/request-access-to-a-vm.png
[5]: ./media/security-center-just-in-time/activity-log.png
[6]: ./media/security-center-just-in-time/default-ports.png
[7]: ./media/security-center-just-in-time/add-a-port.png
[8]: ./media/security-center-just-in-time/edit-policy.png
[9]: ./media/security-center-just-in-time/select-activity-log.png
