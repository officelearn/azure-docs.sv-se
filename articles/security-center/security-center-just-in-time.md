---
title: Just-in-time-VM-åtkomst i Azure Security Center | Microsoft Docs
description: Det här dokumentet beskriver hur just-in-time VM-åtkomst i Azure Security Center hjälper till att du styra åtkomst till virtuella datorer i Azure.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/4/2018
ms.author: rkarlin
ms.openlocfilehash: 94364a54a5a0994cc3de3a2fe014b556d438b2c2
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56114917"
---
# <a name="manage-virtual-machine-access-using-just-in-time"></a>Hantera VM-åtkomst med hjälp av just-in-time

Just-in-time (JIT)-dator (VM) åtkomst kan användas för att låsa inkommande trafik till dina virtuella Azure-datorer minskar exponeringen för attacker samtidigt som det ger enkel åtkomst till att ansluta till virtuella datorer när det behövs.

> [!NOTE]
> Just-in-time-funktionen är tillgänglig på nivån Standard för Security Center.  Mer information om prisalternativen för Security Center finns i [Priser](security-center-pricing.md).
>
>

## <a name="attack-scenario"></a>Attack scenario

Brute force-attacker ofta mål hanteringsportar som ett sätt att få åtkomst till en virtuell dator. Om detta lyckas, kan en angripare ta kontroll över den virtuella datorn och upprätta en fot i din miljö.

Ett sätt att minska exponeringen för en råstyrkeattack är att begränsa hur lång tid att öppna en port. Hanteringsportar behöver inte vara öppna hela tiden. De behöver endast vara öppna medan du är ansluten till den virtuella datorn för att exempelvis utföra hantering eller underhåll. När just-in-time är aktiverat använder Security Center [nätverkssäkerhetsgrupp](../virtual-network/security-overview.md#security-rules) (NSG) regler, vilket begränsar åtkomsten till hanteringsportar så de inte kan nås av angripare.

![Just-in-time-scenario](./media/security-center-just-in-time/just-in-time-scenario.png)

## <a name="how-does-jit-access-work"></a>Hur fungerar JIT-åtkomst?

När just-in-time är aktiverad låser Security Center inkommande trafik till virtuella datorer i Azure genom att skapa en NSG-regel. Du väljer vilka portar på den virtuella datorn som inkommande trafik låses. De här portarna styrs av just-in-time-lösningen.

När en användare begär åtkomst till en virtuell dator, kontrollerar Security Center att användaren har [rollbaserad åtkomstkontroll (RBAC)](../role-based-access-control/role-assignments-portal.md) behörigheter som gör det möjligt för dem att begära har åtkomst till en virtuell dator. Om begäran godkänns konfigurerar Security Center automatiskt Nätverkssäkerhetsgrupper (NSG) för att tillåta inkommande trafik till den valda portar och begärda källans IP-adresser eller intervall för den tid som har angetts. När tiden har gått ut, återställer Security Center NSG: erna till sina tidigare tillstånd. Dessa anslutningar som redan har skapat är inte störs, men.

> [!NOTE]
> Security Center just-in-time-VM åtkomst för närvarande stöder bara virtuella datorer distribueras via Azure Resource Manager. Mer information om klassiskt och Resource Manager distributionsmodellerna finns [Azure Resource Manager och klassisk distribution](../azure-resource-manager/resource-manager-deployment-model.md).
>
>

Du kan komma åt JIT via:
- [Med JIT-åtkomst i Azure Security Center](#jit-asc)
- [Med JIT-åtkomst till en Azure VM-bladet](#jit-vm)

## Med JIT-åtkomst i Azure Security Center <a name="jit-asc"></a>

1. Öppna instrumentpanelen för **Security Center**.

2. I den vänstra rutan väljer **Just-in-time-åtkomst till virtuell dator**.

![Panelen för just-in-time VM-åtkomst](./media/security-center-just-in-time/just-in-time.png)

Den **Just-in-time-åtkomst till virtuell dator** öppnas.

![Panelen för just-in-time VM-åtkomst](./media/security-center-just-in-time/just-in-time-access.png)

**Åtkomst till Virtuella just-in-time** innehåller information om tillståndet för dina virtuella datorer:

- **Konfigurerad** -virtuella datorer som har konfigurerats för att stödja just-in-time åtkomst till virtuell dator. Informationen som presenteras för den senaste veckan och innehåller antalet godkända begäranden, datumet och tiden och senaste användare för varje virtuell dator.
- **Rekommenderade** -virtuella datorer som har stöd för åtkomst till Virtuella just-in-time men inte har konfigurerats för. Vi rekommenderar att du aktiverar just-in-time-åtkomstkontroll för virtuell dator för dessa virtuella datorer. Se [konfigurerar en princip för just-in-time-åtkomst](#jit-config).
- **Ingen rekommendation** – Orsaker som kan orsaka att en virtuell dator inte rekommenderas är:
  - Saknad NSG – just-in-time-lösningen kräver en NSG för att vara på plats.
  - Klassisk virtuell dator – åtkomst till Security Center just-in-time-virtuell dator stöder för närvarande endast datorer som distribuerats via Azure Resource Manager. En klassisk distribution stöds inte av just-in-time-lösningen.
  - Övrigt – en virtuell dator är i den här kategorin om just-in-time-lösningen är avstängd i säkerhetsprincipen för prenumerationen eller resursgruppen, eller att den virtuella datorn saknar en offentlig IP-adress och inte har någon NSG på plats.

### Konfigurera en princip för JIT-åtkomst<a name="jit-config"></a>

Så här väljer du de virtuella datorer som du vill aktivera:

1. Under **Just-in-time-åtkomst till virtuell dator**väljer den **rekommenderas** fliken.

  ![Aktivera just-in-time-åtkomst](./media/security-center-just-in-time/enable-just-in-time-access.png)

2. Under **VM**, Välj de virtuella datorer som du vill aktivera. Detta placerar en bock bredvid en virtuell dator.
3. Välj **aktivera JIT på virtuella datorer**.
  1. Det här bladet visar standardportarna som rekommenderas av Azure Security Center:
     - 22 - SSH
     - 3389 - RDP
     - 5985 - WinRM 
     - 5986 - WinRM
  2. Du kan också konfigurera anpassade portar. Om du vill göra detta, Välj **Lägg till**. 
  3. I **Lägg till Portkonfiguration**, för varje port som du väljer att konfigurera både standard och anpassade, du kan anpassa följande inställningar:
    - **Protokoll-typen**-protokollet som är tillåten på den här porten när en begäran har godkänts.
    - **Tillåtna IP-källadresser**-IP-adressintervall som tillåts på den här porten när en begäran har godkänts.
    - **Maximal begärandetid**-den maximala tidsperioden som en specifik port kan öppnas.

4. Välj **Spara**.


> [!NOTE]
>När JIT VM-åtkomst är aktiverat för en virtuell dator, Azure Security Center skapar neka alla regler för inkommande trafik för de markerade portarna i nätverkssäkerhetsgrupper som är kopplade till den. Reglerna ska antingen vara högsta prioritet för dina Nätverkssäkerhetsgrupper eller lägre prioritet än befintliga regler som redan finns där. Detta beror på en analys som utförs av Azure Security Center som avgör om en regel är säker eller inte.
>

### <a name="request-jit-access-to-a-vm"></a>Begär JIT-åtkomst till en virtuell dator

Att begära åtkomst till en virtuell dator:
1.  Under **Just-in-time-åtkomst till virtuell dator**väljer **konfigurerad**.
2.  Under **VMs**, se de virtuella datorerna som du vill aktivera just-in-time-åtkomst för.
3.  Välj **begär åtkomst**. 
  ![begär åtkomst till virtuell dator](./media/security-center-just-in-time/request-access-to-a-vm.png)
4.  Under **begär åtkomst**för varje virtuell dator, konfigurerar du de portar som du vill öppna och källans IP-adresser som porten är öppen på och på vilket tidsfönster som porten ska öppnas. Det går endast att begära åtkomst till de portar som är konfigurerade i just-in-time-principen. Varje port har en högsta tillåtna tid som härleds från just-in-time-princip.
5.  Välj **öppna portar**.

> [!NOTE]
> Om en användare som begär åtkomst ligger bakom en proxy, alternativet **Min IP** kanske inte fungerar. Du kan behöva definiera fullständig IP-adressintervallet för organisationen.

### <a name="editing-a-jit-access-policy"></a>Redigera en princip för JIT-åtkomst

Du kan ändra en virtuell dators befintlig just-in-time-princip genom att lägga till och konfigurera en ny port att skydda för den virtuella datorn eller genom att ändra alla andra inställningar relaterade till en port som redan skyddas.

Redigera en befintlig just-in-time-princip för en virtuell dator:
1. I den **konfigurerad** fliken, under **VMs**, Välj en virtuell dator du vill lägga till en port genom att klicka på de tre punkterna i raden för den virtuella datorn. 
2. Välj **Redigera**.
3. Under **JIT VM-åtkomstkonfiguration**, du kan redigera de befintliga inställningarna för en port som redan skyddad eller Lägg till en ny anpassad port. Mer information finns i [konfigurerar en princip för just-in-time-åtkomst](#jit-config). 
  ![JIT vm-åtkomst](./media/security-center-just-in-time/edit-policy.png)

## Med JIT-åtkomst till en Azure VM-bladet <a name="jit-vm"></a>

Du kan ansluta till en virtuell dator med JIT direkt från VM-bladet i Azure för din bekvämlighet.

### <a name="configuring-a-just-in-time-access-policy"></a>Konfigurera en princip för just-in-time-åtkomst 
Om du vill göra det enklare att distribuera just-in-time-åtkomst för dina virtuella datorer, kan du ange en virtuell dator bara just-in-time-åtkomst ska tillåtas direkt från den virtuella datorn.

1. I Azure-portalen väljer du **virtuella datorer**.
2. Klicka på den virtuella datorn som du vill begränsa till just-in-time-åtkomst.
3. I menyn klickar du på **Configuration**.
4. Under **precis i tid access** klickar du på **aktivera just-in-time-princip**. 

På så sätt kan just-in-time-åtkomst för den virtuella datorn med hjälp av följande inställningar:

- Windows-servrar:
    - RDP-porten 3389
    - tre timmar av högsta tillåtna åtkomst
    - Tillåtna käll-IP-adresser har angetts till Per begäran
- Linux-servrar:
    - SSH-porten 22
    - tre timmar av högsta tillåtna åtkomst
    - Tillåtna käll-IP-adresser har angetts till Per begäran
     
Om en virtuell dator har redan just-in-time aktiverat när du går till dess konfigurationssidan kommer du att kunna just-in-time är aktiverat och att du kan använda länken för att öppna den i Azure Security Center att visa och ändra inställningarna.

![JIT-config i virtuell dator](./media/security-center-just-in-time/jit-vm-config.png)

### <a name="requesting-jit-access-to-a-vm"></a>Begär JIT-åtkomst till en virtuell dator

När du försöker ansluta till en virtuell dator i Azure-portalen kontrollerar Azure för att se om du har en princip för just-in-time-åtkomst som konfigurerats på den virtuella datorn.

- Om du inte har JIT som konfigurerats på en virtuell dator kan du uppmanas att konfigurera en princip för JIT den.

  ![JIT-fråga](./media/security-center-just-in-time/jit-prompt.png)

- Om du har en JIT-princip som konfigurerats på den virtuella datorn kan du klicka på **begär åtkomst** så att du kan ha åtkomst i enlighet med JIT-princip som angetts för den virtuella datorn.

  ![JIT begär åtkomst](./media/security-center-just-in-time/jit-request-access.png)

## <a name="auditing-jit-access-activity"></a>Granskningsaktiviteter för JIT-åtkomst

Du kan få insikter om VM-aktiviteter med loggsökning. Visa loggar:

1. Under **Just-in-time-åtkomst till virtuell dator**väljer den **konfigurerad** fliken.
2. Under **VMs**, Välj en virtuell dator för att visa information om genom att klicka på de tre punkterna i raden för den virtuella datorn. Då öppnas en meny.
3. Välj **aktivitetsloggen** på menyn. Då öppnas **aktivitetsloggen**.

  ![Välj aktivitetsloggen](./media/security-center-just-in-time/select-activity-log.png)

  **Aktivitetsloggen** en filtrerad vy över tidigare åtgärder för den virtuella datorn tillsammans med tid, datum och prenumeration.

Du kan ladda ned logginformation genom att välja **Klicka här för att hämta alla objekt som CSV**.

Ändra filter och välj **tillämpa** att skapa en sökning och log.


## <a name="permissions-needed-to-configure-and-use-jit"></a>Behörigheter som krävs att konfigurera och använda JIT

Ange dessa behörigheter som krävs för att aktivera en användare att konfigurera eller redigera en JIT-princip för en virtuell dator.

Tilldela dem *åtgärder* för rollen: 
-   På en prenumeration eller resursgrupp omfattning som är associerad med den virtuella datorn:
   - Microsoft.Security/locations/jitNetworkAccessPolicies/write
-    På omfånget för en prenumeration eller resursgrupp eller virtuell dator:
   - Microsoft.Compute/virtualMachines/write 

Ange dessa privilegier så att en användare att begära har JIT-åtkomst till en virtuell dator: Tilldela dem *åtgärder* för användaren:
-   På en prenumeration eller resursgrupp omfattning som är associerad med den virtuella datorn:
   - Microsoft.Security/locations/{the_location_of_the_VM}/jitNetworkAccessPolicies/ initiate/action
-    På omfånget för en prenumeration eller resursgrupp eller virtuell dator:
   - Microsoft.Compute/virtualMachines/read



## <a name="use-jit-programmatically"></a>Använda JIT programmässigt
Du kan konfigurera och använda just-in-time via REST API: er och via PowerShell.

### <a name="using-just-in-time-vm-access-via-rest-apis"></a>Med hjälp av just-in-time VM åtkomst via REST API: er

Funktionen just-in-time VM kan användas via API: et för Azure Security Center. Du kan få information om konfigurerade virtuella datorer, lägga till nya, begära åtkomst till en virtuell dator och mer, via den här API: et. Se [Jit Nätverksåtkomstpolicys](https://docs.microsoft.com/rest/api/securitycenter/jitnetworkaccesspolicies)du vill veta mer om just-in-time-REST-API.

### <a name="using-jit-vm-access-via-powershell"></a>Med JIT VM-åtkomst via PowerShell 

För att använda just-in-time VM access-lösning via PowerShell, använder du officiellt Azure Security Center PowerShell-cmdlets, särskilt `Set-AzureRmJitNetworkAccessPolicy`.

I följande exempel anger en åtkomstprincip för just-in-time-VM på en specifik virtuell dator och anger följande:
1.  Stäng port 22 och 3389.
2.  Ange en maximala tidsperioden på 3 timmar för varje så att de kan öppnas per godkände begäran.
3.  Kan den användare som begär åtkomst för att styra källans IP-adresser och används att upprätta en lyckad session på en godkänd just-in-time åtkomstbegäran.

Kör följande i PowerShell för att åstadkomma detta:

1.  Tilldela en variabel som innehåller åtkomstprincipen för just-in-time-VM för en virtuell dator:

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

2.  Infoga åtkomstprincip för VM-in-time VM till en matris:
    
        $JitPolicyArr=@($JitPolicy)

3.  Konfigurera åtkomstprincip för just-in-time-VM på den valda virtuella datorn:
    
        Set-AzureRmJitNetworkAccessPolicy -Kind "Basic" -Location "LOCATION" -Name "default" -ResourceGroupName "RESOURCEGROUP" -VirtualMachine $JitPolicyArr 

#### <a name="requesting-access-to-a-vm"></a>Begär åtkomst till en virtuell dator

I följande exempel ser du en åtkomstbegäran för just-in-time-VM till en specifik virtuell dator i vilken port 22 har begärts ska öppnas för en specifik IP-adress och för en viss tidsperiod:

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
I den här artikeln har du lärt dig hur just-in-time VM-åtkomst i Security Center hjälper till att du styra åtkomst till virtuella datorer i Azure.

I följande avsnitt kan du lära dig mer om Security Center:

- [Ange säkerhetsprinciper](tutorial-security-policy.md) – Lär dig hur du ställer in säkerhetsprinciper för dina Azure-prenumerationer och resursgrupper.
- [Hantera säkerhetsrekommendationer](security-center-recommendations.md) – Lär dig hur rekommendationer hjälper dig att skydda dina Azure-resurser.
- [Övervakning av säkerhetshälsa](security-center-monitoring.md) – Lär dig att övervaka hälsotillståndet hos dina Azure-resurser.
- [Hantera och åtgärda säkerhetsaviseringar](security-center-managing-and-responding-alerts.md) – Lär dig hur du hanterar och åtgärdar säkerhetsaviseringar.
- [Övervaka partnerlösningar](security-center-partner-solutions.md) – Lär dig hur du övervakar dina partnerlösningars hälsostatus.
- [Security Center vanliga frågor och svar](security-center-faq.md) – hittar du vanliga frågor och svar om tjänsten.
- [Azures säkerhetsblogg](https://blogs.msdn.microsoft.com/azuresecurity/) – Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.

