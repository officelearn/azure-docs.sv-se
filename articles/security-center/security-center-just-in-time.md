---
title: Just-in-time-VM-åtkomst i Azure Security Center | Microsoft Docs
description: Det här dokumentet beskriver hur just-in-time VM-åtkomst i Azure Security Center hjälper till att du styra åtkomst till virtuella datorer i Azure.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 671930b1-fc84-4ae2-bf7c-d34ea37ec5c7
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 6/17/2019
ms.author: v-mohabe
ms.openlocfilehash: eb9366acf82c94bdf99c4d4f0c7c6bdf4f51e06d
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295030"
---
# <a name="manage-virtual-machine-access-using-just-in-time"></a>Hantera VM-åtkomst med hjälp av just-in-time

Just-in-time (JIT)-dator (VM) åtkomst kan användas för att låsa inkommande trafik till dina virtuella Azure-datorer minskar exponeringen för attacker samtidigt som det ger enkel åtkomst till att ansluta till virtuella datorer när det behövs.

> [!NOTE]
> Just-in-time-funktionen är tillgänglig på nivån Standard för Security Center.  Mer information om prisalternativen för Security Center finns i [Priser](security-center-pricing.md).


> [!NOTE]
> Security Center just-in-time-VM åtkomst för närvarande stöder bara virtuella datorer distribueras via Azure Resource Manager. Mer information om klassiskt och Resource Manager distributionsmodellerna finns [Azure Resource Manager och klassisk distribution](../azure-resource-manager/resource-manager-deployment-model.md).

## <a name="attack-scenario"></a>Attack scenario

Brute force-attacker ofta mål hanteringsportar som ett sätt att få åtkomst till en virtuell dator. Om detta lyckas, kan en angripare ta kontroll över den virtuella datorn och upprätta en fot i din miljö.

Ett sätt att minska exponeringen för en råstyrkeattack är att begränsa hur lång tid att öppna en port. Hanteringsportar behöver inte vara öppna hela tiden. De behöver endast vara öppna medan du är ansluten till den virtuella datorn för att exempelvis utföra hantering eller underhåll. När just-in-time är aktiverat använder Security Center [nätverkssäkerhetsgrupp](../virtual-network/security-overview.md#security-rules) (NSG) och Azure brandväggsregler, som begränsar åtkomsten till hanteringsportar så de inte kan nås av angripare.

![Just-in-time-scenario](./media/security-center-just-in-time/just-in-time-scenario.png)

## <a name="how-does-jit-access-work"></a>Hur fungerar JIT-åtkomst?

När just-in-time är aktiverad låser Security Center inkommande trafik till virtuella datorer i Azure genom att skapa en NSG-regel. Du väljer vilka portar på den virtuella datorn som inkommande trafik låses. De här portarna styrs av just-in-time-lösningen.

När en användare begär åtkomst till en virtuell dator, kontrollerar Security Center att användaren har [rollbaserad åtkomstkontroll (RBAC)](../role-based-access-control/role-assignments-portal.md) behörigheter som gör det möjligt för dem att begära har åtkomst till en virtuell dator. Om begäran godkänns konfigurerar Security Center automatiskt Nätverkssäkerhetsgrupper (NSG) och Azure-brandväggen som tillåter inkommande trafik till den valda portar och begärda källans IP-adresser eller intervall för den tid som har angetts. När tiden har gått ut, återställer Security Center NSG: erna till sina tidigare tillstånd. Dessa anslutningar som redan har skapat är inte störs, men.

 > [!NOTE]
 > Om en JIT-åtkomst-begäran har godkänts för en virtuell dator bakom en brandvägg för Azure, ändras Security Center automatiskt både brandvägg och NSG principregler. Den mängd tid som har angetts, tillåter reglerna inkommande trafik till den valda portar och begärda källans IP-adresser eller intervall. När tiden är slut, återställer Security Center brandvägg och NSG-regler till sina tidigare tillstånd.


## <a name="permissions-needed-to-configure-and-use-jit"></a>Behörigheter som krävs att konfigurera och använda JIT

| För att aktivera en användare: | Behörighet för att ange|
| --- | --- |
| Konfigurera eller redigera en JIT-princip för en virtuell dator | *Tilldela de här åtgärderna för rollen:*  På en prenumeration eller resursgrupp omfattning som är associerad med den virtuella datorn: ```Microsoft.Security/locations/jitNetworkAccessPolicies/write``` På omfånget för en prenumeration eller resursgrupp eller virtuell dator: ```Microsoft.Compute/virtualMachines/write``` | 
| ||
|Begär JIT-åtkomst till en virtuell dator | *Tilldela de här åtgärderna för användaren:*  På en prenumeration eller resursgrupp omfattning som är associerad med den virtuella datorn:  ```Microsoft.Security/locations/{the_location_of_the_VM}/jitNetworkAccessPolicies/initiate/action``` På omfånget för en prenumeration eller resursgrupp eller virtuell dator: ```Microsoft.Compute/virtualMachines/read``` |


## <a name="configure-jit-on-a-vm"></a>Konfigurera JIT på en virtuell dator

Det finns 3 sätt att konfigurera en JIT-princip på en virtuell dator:

- [Konfigurera JIT-åtkomst i Azure Security Center](#jit-asc)
- [Konfigurera JIT-åtkomst i en Azure VM-bladet](#jit-vm)
- [Konfigurera en princip för JIT på en virtuell dator via programmering](#jit-program)

## <a name="configure-jit-in-asc"></a>Konfigurera JIT i ASC

ASC, kan du konfigurera en JIT-principen och begär åtkomst till en virtuell dator med en JIT-princip


### Konfigurera JIT-åtkomst på en virtuell dator i ASC <a name="jit-asc"></a>

1. Öppna instrumentpanelen för **Security Center**.

2. I den vänstra rutan väljer **Just-in-time-åtkomst till virtuell dator**.

    ![Panelen för just-in-time VM-åtkomst](./media/security-center-just-in-time/just-in-time.png)

    Den **Just-in-time-åtkomst till virtuell dator** öppnas.

      ![Aktivera just-in-time-åtkomst](./media/security-center-just-in-time/enable-just-in-time.png)

    **Åtkomst till Virtuella just-in-time** innehåller information om tillståndet för dina virtuella datorer:

    - **Konfigurerad** -virtuella datorer som har konfigurerats för att stödja just-in-time åtkomst till virtuell dator. Informationen som presenteras för den senaste veckan och innehåller antalet godkända begäranden, datumet och tiden och senaste användare för varje virtuell dator.
    - **Rekommenderade** -virtuella datorer som har stöd för åtkomst till Virtuella just-in-time men inte har konfigurerats för. Vi rekommenderar att du aktiverar just-in-time-åtkomstkontroll för virtuell dator för dessa virtuella datorer. 
    - **Ingen rekommendation** – Orsaker som kan orsaka att en virtuell dator inte rekommenderas är:
      - Saknad NSG – just-in-time-lösningen kräver en NSG för att vara på plats.
      - Klassisk virtuell dator – åtkomst till Security Center just-in-time-virtuell dator stöder för närvarande endast datorer som distribuerats via Azure Resource Manager. En klassisk distribution stöds inte av just-in-time-lösningen. 
      - Övrigt – en virtuell dator är i den här kategorin om just-in-time-lösningen är avstängd i säkerhetsprincipen för prenumerationen eller resursgruppen, eller om den virtuella datorn saknar en offentlig IP-adress och inte har någon NSG på plats.

3. Välj den **rekommenderas** fliken.

4. Under **VM**, klicka på de virtuella datorer som du vill aktivera. Detta placerar en bock bredvid en virtuell dator.

5. Klicka på **aktivera JIT på virtuella datorer**.
   -. Det här bladet visar standardportarna som rekommenderas av Azure Security Center:
      - 22 - SSH
      - 3389 - RDP
      - 5985 - WinRM 
      - 5986 - WinRM
6. Du kan också konfigurera anpassade portar:

      1. Klicka på **Lägg till**. Den **Lägg till Portkonfiguration** öppnas.
      2. För varje port du vill konfigurera, både standard och anpassade, du kan anpassa följande inställningar:

    - **Protokoll-typen**-protokollet som är tillåten på den här porten när en begäran har godkänts.
    - **Tillåtna IP-källadresser**-IP-adressintervall som tillåts på den här porten när en begäran har godkänts.
    - **Maximal begärandetid**-den maximala tidsperioden som en specifik port kan öppnas.

     3. Klicka på **OK**.

1. Klicka på **Spara**.

> [!NOTE]
>När JIT VM-åtkomst är aktiverat för en virtuell dator, skapar Azure Security Center ”neka all inkommande trafik” regler för de valda portarna i Azure-brandvägg och nätverkssäkerhetsgrupper som är associerade med den. Om andra regler som hade skapats för de valda portarna, sedan åsidosätter de befintliga reglerna nya regler för ”neka all inkommande trafik”. Om det finns inga befintliga regler på de markerade portarna, ta nya regler för ”neka all inkommande trafik” högsta prioritet i Azure-brandvägg och Nätverkssäkerhetsgrupper.


## <a name="request-jit-access-via-asc"></a>Begär JIT-åtkomst via ASC

Att begära åtkomst till en virtuell dator via ASC:

1. Under **Just-in-time-åtkomst till virtuell dator**väljer den **konfigurerad** fliken.

2. Under **VM**, klicka på de virtuella datorerna som du vill få åtkomst till. Detta placerar en bock bredvid den virtuella datorn.


    - Ikonen i den **anslutningsinformationen** kolumnen anger om JIT har aktiverats på NSG eller VB. Om den är aktiverad på både, visas endast ikonen brandväggen.

    - Den **anslutningsinformationen** kolumnen innehåller rätt information som krävs för att ansluta den virtuella datorn, samt anger öppnade portar.

      ![Begära just-in-time-åtkomst](./media/security-center-just-in-time/request-just-in-time-access.png)

3. Klicka på **begär åtkomst**. Den **begär åtkomst** öppnas.

      ![JIT-information](./media/security-center-just-in-time/just-in-time-details.png)

4. Under **begär åtkomst**för varje virtuell dator, konfigurerar du de portar som du vill öppna och källans IP-adresser som porten är öppen på och på vilket tidsfönster som porten ska öppnas. Det går endast att begära åtkomst till de portar som är konfigurerade i just-in-time-principen. Varje port har en högsta tillåtna tid som härleds från just-in-time-princip.

5. Klicka på **öppna portar**.

> [!NOTE]
> Om en användare som begär åtkomst ligger bakom en proxy, alternativet **Min IP** kanske inte fungerar. Du kan behöva definiera fullständig IP-adressintervallet för organisationen.

## <a name="edit-a-jit-access-policy-via-asc"></a>Redigera en princip för JIT-åtkomst via ASC

Du kan ändra en virtuell dators befintlig just-in-time-princip genom att lägga till och konfigurera en ny port att skydda för den virtuella datorn eller genom att ändra alla andra inställningar relaterade till en port som redan skyddas.

Redigera en befintlig just-in-time-princip för en virtuell dator:
1. I den **konfigurerad** fliken, under **VMs**, Välj en virtuell dator du vill lägga till en port genom att klicka på de tre punkterna i raden för den virtuella datorn. 

1. Välj **Redigera**.
1. Under **JIT VM-åtkomstkonfiguration**, du kan redigera de befintliga inställningarna för en port som redan skyddad eller Lägg till en ny anpassad port. 
  ![JIT vm-åtkomst](./media/security-center-just-in-time/edit-policy.png)

## <a name="audit-jit-access-activity-in-asc"></a>Granska aktivitet för JIT-åtkomst i ASC

Du kan få insikter om VM-aktiviteter med loggsökning. Visa loggar:

1. Under **Just-in-time-åtkomst till virtuell dator**väljer den **konfigurerad** fliken.
2. Under **VMs**, Välj en virtuell dator för att visa information om genom att klicka på de tre punkterna i raden för den virtuella datorn och välj **aktivitetsloggen** på menyn. Den **aktivitetsloggen** öppnas.

   ![Välj aktivitetsloggen](./media/security-center-just-in-time/select-activity-log.png)

   **Aktivitetsloggen** en filtrerad vy över tidigare åtgärder för den virtuella datorn tillsammans med tid, datum och prenumeration.

Du kan ladda ned logginformation genom att välja **Klicka här för att hämta alla objekt som CSV**.

Ändra filtren och klicka på **tillämpa** att skapa en sökning och log.



## Konfigurera JIT-åtkomst i en Azure VM-bladet <a name="jit-vm"></a>

Du kan ansluta till en virtuell dator med JIT direkt från VM-bladet i Azure för din bekvämlighet.

### <a name="configure-jit-access-on-a-vm-via-the-azure-vm-blade"></a>Konfigurera JIT-åtkomst på en virtuell dator via Azure VM-bladet

Om du vill göra det enklare att distribuera just-in-time-åtkomst för dina virtuella datorer, kan du ange en virtuell dator bara just-in-time-åtkomst ska tillåtas direkt från den virtuella datorn.

1. I Azure-portalen väljer du **virtuella datorer**.
2. Klicka på den virtuella datorn som du vill begränsa till just-in-time-åtkomst.
3. I menyn klickar du på **Configuration**.
4. Under **precis i tid access** klickar du på **aktivera just-in-time-princip**. 

På så sätt kan just-in-time-åtkomst för den virtuella datorn med hjälp av följande inställningar:

- Windows-servrar:
    - RDP-porten 3389
    - tre timmar av högsta tillåtna åtkomst
    - Tillåtna käll-IP-adresser har angetts till ett
- Linux-servrar:
    - SSH-porten 22
    - tre timmar av högsta tillåtna åtkomst
    - Tillåtna käll-IP-adresser har angetts till ett
     
Om en virtuell dator har redan just-in-time aktiverat när du går till dess konfigurationssidan kommer du att kunna just-in-time är aktiverat och att du kan använda länken för att öppna den i Azure Security Center att visa och ändra inställningarna.

![JIT-config i virtuell dator](./media/security-center-just-in-time/jit-vm-config.png)

### <a name="request-jit-access-to-a-vm-via-the-azure-vm-blade"></a>Begär JIT-åtkomst till en virtuell dator via Azure VM-bladet

När du försöker ansluta till en virtuell dator i Azure-portalen kontrollerar Azure för att se om du har en princip för just-in-time-åtkomst som konfigurerats på den virtuella datorn.

- Om du har en JIT-princip som konfigurerats på den virtuella datorn kan du klicka på **begär åtkomst** så att du kan ha åtkomst i enlighet med JIT-princip som angetts för den virtuella datorn. 

  >![JIT-begäran](./media/security-center-just-in-time/jit-request.png)

  Åtkomst har begärts med följande standardparametrar:

  - **käll-IP**: 'Valfritt' (*) (det går inte att ändras)
  - **tidsintervall**: 3 timmar (det går inte att ändras)  <!--Isn't this set in the policy-->
  - **portnummer** RDP port 3389 för Windows / port 22 för Linux (kan ändras)

    > [!NOTE]
    > När en begäran har godkänts för en virtuell dator som skyddas av Brandvägg för Azure tillhandahåller Security Center användaren med rätt anslutningsinformationen (portmappning från tabellen DNAT) om du vill använda för att ansluta till den virtuella datorn.

- Om du inte har JIT som konfigurerats på en virtuell dator kan du uppmanas att konfigurera en princip för JIT den.

  ![JIT-fråga](./media/security-center-just-in-time/jit-prompt.png)

## Konfigurera en princip för JIT på en virtuell dator via programmering  <a name="jit-program"></a>

Du kan konfigurera och använda just-in-time via REST API: er och via PowerShell.

## <a name="jit-vm-access-via-rest-apis"></a>JIT VM-åtkomst via REST API: er

Funktionen just-in-time VM kan användas via API: et för Azure Security Center. Du kan få information om konfigurerade virtuella datorer, lägga till nya, begära åtkomst till en virtuell dator och mer, via den här API: et. Se [Jit Nätverksåtkomstpolicys](https://docs.microsoft.com/rest/api/securitycenter/jitnetworkaccesspolicies)du vill veta mer om just-in-time-REST-API.

## <a name="jit-vm-access-via-powershell"></a>JIT VM-åtkomst via PowerShell

För att använda just-in-time VM access-lösning via PowerShell, använder du officiellt Azure Security Center PowerShell-cmdlets, särskilt `Set-AzJitNetworkAccessPolicy`.

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
    
        Set-AzJitNetworkAccessPolicy -Kind "Basic" -Location "LOCATION" -Name "default" -ResourceGroupName "RESOURCEGROUP" -VirtualMachine $JitPolicyArr 

#### <a name="request-access-to-a-vm-via-powershell"></a>Begär åtkomst till en virtuell dator via PowerShell

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

        Start-AzJitNetworkAccessPolicy -ResourceId "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Security/locations/LOCATION/jitNetworkAccessPolicies/default" -VirtualMachine $JitPolicyArr

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

