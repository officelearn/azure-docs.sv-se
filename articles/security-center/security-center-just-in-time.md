---
title: Just-in-time-VM åtkomst i Azure Security Center | Microsoft Docs
description: Det här dokumentet visar hur just-in-time-åtkomst till virtuell dator i Azure Security Center hjälper dig att styra åtkomsten till virtuella datorer i Azure.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2018
ms.author: rkarlin
ms.openlocfilehash: 98533e3c1454867ff09c53902f0f575d198452a3
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2018
ms.locfileid: "49320347"
---
# <a name="manage-virtual-machine-access-using-just-in-time"></a>Hantera VM-åtkomst med hjälp av just-in-time

Just-in-time-dator (VM) kan åtkomst användas för att låsa inkommande trafik till dina virtuella Azure-datorer minskar exponeringen för attacker samtidigt som det ger enkel åtkomst till att ansluta till virtuella datorer när det behövs.

> [!NOTE]
> Just in-time-funktionen är tillgänglig för standardnivån i Security Center.  Mer information om prisalternativen för Security Center finns i [Priser](security-center-pricing.md).
>
>

## <a name="attack-scenario"></a>Attack scenario

Brute force-attacker ofta mål hanteringsportar som ett sätt att få åtkomst till en virtuell dator. Om detta lyckas, kan en angripare ta kontroll över den virtuella datorn och upprätta en fot i din miljö.

Ett sätt att minska exponeringen för en råstyrkeattack är att begränsa hur lång tid att öppna en port. Hanteringsportar behöver inte vara öppna hela tiden. De behöver endast vara öppna medan du är ansluten till den virtuella datorn för att exempelvis utföra hantering eller underhåll. När just-in-time är aktiverat, Security Center använder [nätverkssäkerhetsgrupp](../virtual-network/security-overview.md#security-rules) (NSG) regler, vilket begränsar åtkomsten till hanteringsportar så de inte kan nås av angripare.

![Just-in-time-scenario][1]

## <a name="how-does-just-in-time-access-work"></a>Hur just-in-time-åtkomst till fungerar?

När JIT (Just-In-Time) är aktiverat begränsar Security Center inkommande trafik till dina virtuella Azure-datorer genom att skapa en NSG-regel. Du väljer vilka portar på den virtuella datorn som inkommande trafik låses. De här portarna styrs av just i time-lösningen.

När en användare begär åtkomst till en virtuell dator, kontrollerar Security Center att användaren har [rollbaserad åtkomstkontroll (RBAC)](../role-based-access-control/role-assignments-portal.md) behörigheter som ger skrivbehörighet för den virtuella datorn. Om de har skrivbehörighet, begäran har godkänts och konfigurerar Security Center automatiskt Nätverkssäkerhetsgrupper (NSG) för att tillåta inkommande trafik till de markerade portarna för den tid angett du. När tiden har gått ut, återställer Security Center NSG: erna till sina tidigare tillstånd. Dessa anslutningar som redan har skapat är inte störs, men.

> [!NOTE]
> Security Center just-in-time-åtkomst till virtuell dator stöder för närvarande endast datorer som distribuerats via Azure Resource Manager. Mer information om klassiskt och Resource Manager distributionsmodellerna finns [Azure Resource Manager och klassisk distribution](../azure-resource-manager/resource-manager-deployment-model.md).
>
>

## <a name="using-just-in-time-access"></a>Med hjälp av just-in-time-åtkomst

1. Öppna instrumentpanelen för **Security Center**.

2. I den vänstra rutan väljer **Just-in-time-åtkomst till virtuell dator**.

![Just-in-time-åtkomst till virtuell dator i panelen][2]

Den **Just-in-time-åtkomst till virtuell dator** öppnas.

![Just-in-time-åtkomst till virtuell dator i panelen][10]

**Just-in-time-åtkomst till virtuell dator** tillhandahåller information om dina virtuella datorers status:

- **Konfigurerad** – Virtuella datorer som har konfigurerats för att stödja Just-in-time-åtkomst till virtuella datorer. Informationen som presenteras för den senaste veckan och innehåller antalet godkända begäranden, datumet och tiden och senaste användare för varje virtuell dator.
- **Rekommenderas** – Virtuella datorer som kan stödja Just-in-time-åtkomst till virtuell dator men som inte har konfigurerats för det. Vi rekommenderar att du aktiverar just-in-time-åtkomstkontroll för virtuell dator för dessa virtuella datorer. Se [konfigurerar bara i tid åtkomstprincip](#configuring-a-just-in-time-access-policy).
- **Ingen rekommendation** – Orsaker som kan orsaka att en virtuell dator inte rekommenderas är:
  - Saknad NSG – Just-in-time-lösningen kräver att det finns en NSG.
  - Klassisk virtuell dator – Security Centers just-in-time-åtkomst till virtuell dator stöder för närvarande bara virtuella datorer som har distribuerats via Azure Resource Manager. En klassisk distribution stöds inte av just i time-lösningen.
  - Övrigt – En virtuell dator i den här kategorin om just-in-time-lösningen är avstängd i säkerhetsprincipen för prenumerationen eller resursgruppen, eller om den virtuella datorn saknar en offentlig IP-adress och inte har någon NSG.

## <a name="configuring-a-just-in-time-access-policy"></a>Konfigurera bara i tid åtkomstprincip

Så här väljer du de virtuella datorer som du vill aktivera:

1. Under **Just-in-time-åtkomst till virtuell dator**väljer den **rekommenderas** fliken.

  ![Aktivera just-in-time-åtkomst][3]

2. Under **VM**, Välj de virtuella datorer som du vill aktivera. Detta placerar en bock bredvid en virtuell dator.
3. Välj **aktivera JIT på virtuella datorer**.
4. Välj **Spara**.

### <a name="default-ports"></a>Standardportar

Du kan se standardportarna som Security Center rekommenderar att aktivera just-in-time.

1. Under **Just-in-time-åtkomst till virtuell dator**väljer den **rekommenderas** fliken.

  ![Visa standardportar][6]

2. Under **VMs**, Välj en virtuell dator. Detta placerar en bock bredvid den virtuella datorn och öppnar **JIT VM-åtkomstkonfiguration**. Det här bladet visar standardportarna.

### <a name="add-ports"></a>Lägg till portar

Under **JIT VM-åtkomstkonfiguration**, du kan också lägga till och konfigurera en ny port som du vill aktivera just i time-lösningen.

1. Under **JIT VM-åtkomstkonfiguration**väljer **Lägg till**. Då öppnas **Lägg till Portkonfiguration**.

  ![Portkonfiguration][7]

2. Under **Lägg till Portkonfiguration**, du identifiera port, protokolltyp, tillåtna käll-IP-adresser och maximal begärandetid.

  Tillåtna käll-IP-adresser är IP-adressintervall som tillåts att få åtkomst vid en godkänd förfrågan.

  Maximal begärandetid är den maximala tidsperioden som en specifik port kan öppnas.

3. Välj **OK**.

> [!NOTE]
>När JIT VM-åtkomst är aktiverat för en virtuell dator, Azure Security Center skapar neka alla regler för inkommande trafik för de markerade portarna i nätverkssäkerhetsgrupper som är kopplade till den. Reglerna ska antingen vara högsta prioritet för dina Nätverkssäkerhetsgrupper eller lägre prioritet än befintliga regler som redan finns där. Detta beror på en analys som utförs av Azure Security Center som avgör om en regel är säker eller inte.
>


## <a name="set-just-in-time-within-a-vm"></a>Ange just-in-time i en virtuell dator

Om du vill göra det enklare att distribuera just-in-time-åtkomst för dina virtuella datorer, kan du ange en virtuell dator bara just-in-time-åtkomst ska tillåtas direkt från den virtuella datorn.

1. I Azure-portalen väljer du **virtuella datorer**.
2. Klicka på den virtuella datorn som du vill begränsa till just-in-time-åtkomst.
3. I menyn klickar du på **Configuration**.
4. Under **precis i tid access** klickar du på **aktivera just-in-time-princip**. 

På så sätt kan just-in-time-åtkomst för den virtuella datorn med hjälp av följande inställningar:

- Windows-servrar:
    - RDP-porten 3389
    - tre timmar av åtkomst
    - Tillåtna käll-IP-adresser har angetts till Per begäran
- Linux-servrar:
    - SSH-porten 22
    - tre timmar av åtkomst
    - Tillåtna käll-IP-adresser har angetts till Per begäran
     
Om en virtuell dator har redan just-in-time aktiverat när du går till dess konfigurationssidan kommer du att kunna just-in-time är aktiverat och att du kan använda länken för att öppna den i Azure Security Center att visa och ändra inställningarna.

![JIT-config i virtuell dator](./media/security-center-just-in-time/jit-vm-config.png)


## <a name="requesting-access-to-a-vm"></a>Begär åtkomst till en virtuell dator

Att begära åtkomst till en virtuell dator:

1. Under **Just-in-time-åtkomst till virtuell dator**väljer den **konfigurerad** fliken.
2. Under **VMs**, Välj de virtuella datorerna som du vill aktivera åtkomst. Detta placerar en bock bredvid en virtuell dator.
3. Välj **begär åtkomst**. Då öppnas **begär åtkomst**.

  ![Begär åtkomst till en virtuell dator][4]

4. Under **begär åtkomst**, du konfigurerar för varje virtuell dator portar öppna tillsammans med den IP-källan som porten är öppen till och tidsfönstret porten är öppen. Du kan begära åtkomst endast för de portar som konfigurerats i just i time-princip. Varje port har en högsta tillåtna tid som härletts från just i time-princip.
5. Välj **öppna portar**.

> [!NOTE]
> När en användare begär åtkomst till en virtuell dator, kontrollerar Security Center att användaren har [rollbaserad åtkomstkontroll (RBAC)](../role-based-access-control/role-assignments-portal.md) behörigheter som ger skrivbehörighet för den virtuella datorn. Om de har skrivbehörighet har begäran godkänts.
>
>

> [!NOTE]
> Om en användare som begär åtkomst är bakom en proxyserver, fungerar inte alternativet ”Min IP”. Det kan finnas ett behov av att definiera ett komplett utbud av organisationen.
>
>

## <a name="editing-a-just-in-time-access-policy"></a>Redigera bara i tid åtkomstprincip

Du kan ändra en virtuell dators befintliga just-in-time-princip genom att lägga till och konfigurera en ny port att öppna för den virtuella datorn eller genom att ändra andra parametrar som är relaterade till en port som redan skyddas.

För att redigera en befintlig just-in-time-princip för en virtuell dator i **konfigurerad** fliken används:

1. Under **VMs**, Välj en virtuell dator att lägga till en port för genom att klicka på de tre punkterna i raden för den virtuella datorn. Då öppnas en meny.
2. Välj **redigera** på menyn. Då öppnas **JIT VM-åtkomstkonfiguration**.

  ![Redigera princip][8]

3. Under **JIT VM-åtkomstkonfiguration**, du kan antingen redigera de befintliga inställningarna för en port som redan skyddas genom att klicka på dess port eller du kan välja **Lägg till**. Då öppnas **Lägg till Portkonfiguration**.

  ![Lägga till en port][7]

4. Under **Lägg till Portkonfiguration**, identifiera porten och protokoll-typ, tillåtna käll-IP-adresser och maximal tid för begäran.
5. Välj **OK**.
6. Välj **Spara**.

## <a name="auditing-just-in-time-access-activity"></a>Granskning just-in-time-åtkomstaktivitet

Du kan få insikter om VM-aktiviteter med loggsökning. Visa loggar:

1. Under **Just-in-time-åtkomst till virtuell dator**väljer den **konfigurerad** fliken.
2. Under **VMs**, Välj en virtuell dator för att visa information om genom att klicka på de tre punkterna i raden för den virtuella datorn. Då öppnas en meny.
3. Välj **aktivitetsloggen** på menyn. Då öppnas **aktivitetsloggen**.

  ![Välj aktivitetsloggen][9]

  **Aktivitetsloggen** en filtrerad vy över tidigare åtgärder för den virtuella datorn tillsammans med tid, datum och prenumeration.

Du kan ladda ned logginformation genom att välja **Klicka här för att hämta alla objekt som CSV**.

Ändra filter och välj **tillämpa** att skapa en sökning och log.

## <a name="using-just-in-time-vm-access-via-rest-apis"></a>Med hjälp av just-in-time-åtkomst till virtuell dator via REST API: er

Just i time-åtkomst till VM-funktionen kan användas via API: et för Azure Security Center. Du kan få information om konfigurerade virtuella datorer, lägga till nya, begära åtkomst till en virtuell dator och mer, via den här API: et. Se [Jit Nätverksåtkomstpolicys](https://docs.microsoft.com/rest/api/securitycenter/jitnetworkaccesspolicies)du vill veta mer om just tidpunkt REST API.

## <a name="using-just-in-time-vm-access-via-powershell"></a>Med hjälp av just-in-time-åtkomst till virtuell dator via PowerShell 

Du använder den bara i tid VM lösning för åtkomst via PowerShell, använder du officiellt Azure Security Center PowerShell-cmdlets, särskilt `Set-AzureRmJitNetworkAccessPolicy`.

I följande exempel anger bara i tid VM åtkomstprincipen på en specifik virtuell dator och anger följande:
1.  Stäng port 22 och 3389.
2.  Ange en maximala tidsperioden på 3 timmar för varje så att de kan öppnas per godkände begäran.
3.  Användaren som begär åtkomst för att styra källan IP-adresser och används att upprätta en lyckad session på en godkänd just-in-time-åtkomstbegäran.

Kör följande i PowerShell för att åstadkomma detta:

1.  Tilldela en variabel som innehåller just tidpunkt VM-åtkomstprincip för en virtuell dator:

        $JitPolicy = (@{
         id="/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME"
        ports=(@{
             number=22;
             protocol="*";
             allowedSourceAddressPrefix=@("*");
             maxRequestAccessDuration="PT3H"},
             @{
             number=3389;
             protocol="*";
             allowedSourceAddressPrefix=@("*");
             maxRequestAccessDuration="PT3H"})})

2.  Infoga den virtuella datorn just-in-time åtkomstprincip för virtuell dator till en matris:
    
        $JitPolicyArr=@($JitPolicy)

3.  Konfigurera just tidpunkt VM åtkomstprincipen på den valda virtuella datorn:
    
        Set-AzureRmJitNetworkAccessPolicy -Kind "Basic" -Location "LOCATION" -Name "default" -ResourceGroupName "RESOURCEGROUP" -VirtualMachine $JitPolicyArr 

### <a name="requesting-access-to-a-vm"></a>Begär åtkomst till en virtuell dator

I följande exempel visas bara i tid VM åtkomstbegäran till en specifik virtuell dator i vilken port 22 har begärts ska öppnas för en specifik IP-adress och för en viss tidsperiod:

Kör följande i PowerShell:
1.  Konfigurera virtuella datorns egenskaper för åtkomst av begäran

        $JitPolicyVm1 = (@{
          id="/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME"
        ports=(@{
           number=22;
           endTimeUtc="2018-09-17T17:00:00.3658798Z";
           allowedSourceAddressPrefix=@("IPV4ADDRESS")})})
2.  Infoga VM åtkomst parametrarna i en matris:

        $JitPolicyArr=@($JitPolicyVm1)
3.  Skicka åtkomstbegäran (Använd resurs-ID du fick i steg 1)

        Start-AzureRmJitNetworkAccessPolicy -ResourceId "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Security/locations/LOCATION/jitNetworkAccessPolicies/default" -VirtualMachine $JitPolicyArr

Mer information finns i dokumentationen för PowerShell-cmdlet.


## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig hur just-in-time-åtkomst till virtuell dator i Security Center hjälper till att du styra åtkomsten till din Azure-datorer.

I följande avsnitt kan du lära dig mer om Security Center:

- [Ange säkerhetsprinciper](security-center-policies.md) – Lär dig hur du ställer in säkerhetsprinciper för dina Azure-prenumerationer och resursgrupper.
- [Hantera säkerhetsrekommendationer](security-center-recommendations.md) – Lär dig hur rekommendationer hjälper dig att skydda dina Azure-resurser.
- [Övervakning av säkerhetshälsa](security-center-monitoring.md) – Lär dig att övervaka hälsotillståndet hos dina Azure-resurser.
- [Hantera och åtgärda säkerhetsaviseringar](security-center-managing-and-responding-alerts.md) – Lär dig hur du hanterar och åtgärdar säkerhetsaviseringar.
- [Övervaka partnerlösningar](security-center-partner-solutions.md) – Lär dig hur du övervakar dina partnerlösningars hälsostatus.
- [Security Center vanliga frågor och svar](security-center-faq.md) – hittar du vanliga frågor och svar om tjänsten.
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
