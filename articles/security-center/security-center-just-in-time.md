---
title: Åtkomst till virtuella datorer i Azure Security Center | Microsoft-dokument
description: Det här dokumentet visar hur just-in-time VM-åtkomst i Azure Security Center hjälper dig att kontrollera åtkomsten till dina virtuella Azure-datorer.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 4b2b388fb736997010a6cbbdf93b23b77c7ef3a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77603984"
---
# <a name="secure-your-management-ports-with-just-in-time-access"></a>Säkra hanteringsportarna med just-in-time-åtkomst

Om du använder Security Centers standardprisnivå (se [prissättning)](/azure/security-center/security-center-pricing)kan du låsa inkommande trafik till dina virtuella Azure-datorer med ny tidsåtkomst (JIT). Detta minskar exponeringen för attacker samtidigt som det ger enkel åtkomst till virtuella datorer när det behövs.

> [!NOTE]
> Säkerhetscenter just-in-time VM-åtkomst stöder för närvarande endast virtuella datorer som distribueras via Azure Resource Manager. Mer information om de klassiska distributionsmodellerna och Resurshanterarens distributionsmodeller finns i [Azure Resource Manager jämfört med klassisk distribution](../azure-resource-manager/management/deployment-models.md).

[!INCLUDE [security-center-jit-description](../../includes/security-center-jit-description.md)]

## <a name="configure-jit-on-a-vm"></a>Konfigurera JIT på en virtuell dator

Det finns tre sätt att konfigurera en JIT-princip på en virtuell dator:

- [Konfigurera JIT-åtkomst i Azure Security Center](#jit-asc)
- [Konfigurera JIT-åtkomst på en Azure VM-sida](#jit-vm)
- [Konfigurera en JIT-princip på en virtuell dator programmässigt](#jit-program)

## <a name="configure-jit-in-azure-security-center"></a>Konfigurera JIT i Azure Security Center

Från Security Center kan du konfigurera en JIT-princip och begära åtkomst till en virtuell dator med hjälp av en JIT-princip

### <a name="configure-jit-access-on-a-vm-in-security-center"></a>Konfigurera JIT-åtkomst på en virtuell dator i Security Center<a name="jit-asc"></a>

1. Öppna instrumentpanelen för **Security Center**.

1. Välj Åtkomst till **just-in-time-vm i**den vänstra rutan .

    ![Åtkomstpanel för just-in-time-åtkomst](./media/security-center-just-in-time/just-in-time.png)

    Åtkomstfönstret **just-in-time-åtkomst** öppnas och visar information om tillståndet för dina virtuella datorer:

    - **Konfigurerad** - virtuella datorer som har konfigurerats för att stödja ny vm-åtkomst. De data som presenteras är för den senaste veckan och inkluderar för varje virtuell dator antalet godkända begäranden, senaste åtkomstdatum och åtkomsttid och sista användare.
    - **Rekommenderas** - virtuella datorer som kan stödja just-in-time VM-åtkomst men inte har konfigurerats till. Vi rekommenderar att du aktiverar ny vm-åtkomstkontroll för dessa virtuella datorer.
    - **Ingen rekommendation** – Orsaker som kan orsaka att en virtuell dator inte rekommenderas är:
      - Saknad NSG - Just-in-time-lösningen kräver att en NSG är på plats.
      - Klassisk virtuell dator – säkerhetscenter just-in-time VM-åtkomst stöder för närvarande endast virtuella datorer som distribueras via Azure Resource Manager. En klassisk distribution stöds inte av just-in-time-lösningen. 
      - Annat - En virtuell dator finns i den här kategorin om just-in-time-lösningen är inaktiverad i säkerhetsprincipen för prenumerationen eller resursgruppen, eller om den virtuella datorn saknar en offentlig IP och inte har en NSG på plats.

1. Välj fliken **Rekommenderad.**

1. Klicka på de virtuella datorer som du vill aktivera under **VIRTUELL DATOR.** Detta placerar en bock bredvid en virtuell dator.

      ![Aktivera åtkomst just-in-time](./media/security-center-just-in-time/enable-just-in-time.png)

1. Klicka på **Aktivera JIT på virtuella datorer**. En ruta öppnas med de standardportar som rekommenderas av Azure Security Center:
    - 22 - SSH
    - 3389 - RDP
    - 5985 - WinRM 
    - 5986 - WinRM
1. Du kan också lägga till anpassade portar i listan:

      1. Klicka på **Lägg till**. Fönstret **Lägg till portkonfiguration** öppnas.
      1. För varje port som du väljer att konfigurera, både standard och anpassad, kan du anpassa följande inställningar:
            - **Protokolltyp**- Det protokoll som tillåts på den här porten när en begäran godkänns.
            - **Tillåtna käll-IP-adresser**- IP-intervall som tillåts på den här porten när en begäran godkänns.
            - **Maximal begärandetid**- Det maximala tidsfönstret under vilket en viss port kan öppnas.

     1. Klicka på **OK**.

1. Klicka på **Spara**.

> [!NOTE]
>När JIT VM Access är aktiverat för en virtuell dator, azure security center skapar "neka all inkommande trafik" regler för de valda portarna i nätverkssäkerhetsgrupper associerade och Azure Firewall med den. Om andra regler hade skapats för de valda portarna har de befintliga reglerna företräde framför de nya reglerna "neka all inkommande trafik". Om det inte finns några befintliga regler för de valda portarna har de nya reglerna "neka all inkommande trafik" högsta prioritet i nätverkssäkerhetsgrupperna och Azure-brandväggen.


## <a name="request-jit-access-via-security-center"></a>Begär JIT-åtkomst via Security Center

Så här begär du åtkomst till en virtuell dator via Security Center:

1. Under **Ny vm-åtkomst**väljer du fliken **Konfigurerad.**

1. Klicka på de virtuella datorer som du vill begära åtkomst för under **Virtuell dator.** Detta placerar en bock bredvid den virtuella datorn.

    - Ikonen i kolumnen **Anslutningsinformation** anger om JIT är aktiverat på NSG eller FW. Om den är aktiverad på båda visas bara brandväggsikonen.

    - Kolumnen **Anslutningsinformation** innehåller den information som krävs för att ansluta den virtuella datorn och dess öppna portar.

      ![Begära just-in-time-åtkomst](./media/security-center-just-in-time/request-just-in-time-access.png)

1. Klicka på **Åtkomst till begäran**. Fönstret **Begär åtkomst** öppnas.

      ![Jit-detaljer](./media/security-center-just-in-time/just-in-time-details.png)

1. Under **Begäranåtkomst**konfigurerar du de portar som du vill öppna och de käll-IP-adresser som porten öppnas för och det tidsfönster som porten ska vara öppen för. Det går bara att begära åtkomst till de portar som har konfigurerats i just-in-time-principen. Varje port har en maximal tillåten tid som härleds från just-in-time-principen.

1. Klicka på **Öppna portar**.

> [!NOTE]
> Om en användare som begär åtkomst ligger bakom en proxy kanske alternativet **Min IP** inte fungerar. Du kan behöva definiera hela IP-adressintervallet för organisationen.



## <a name="edit-a-jit-access-policy-via-security-center"></a>Redigera en JIT-åtkomstprincip via Security Center

Du kan ändra en virtuell dators befintliga just-in-time-princip genom att lägga till och konfigurera en ny port för att skydda för den virtuella datorn, eller genom att ändra någon annan inställning som är relaterad till en redan skyddad port.

Så här redigerar du en befintlig just-in-time-princip för en virtuell dator:

1. På fliken **Konfigurerad** under **virtuella datorer**väljer du en virtuell dator som du vill lägga till en port på genom att klicka på de tre punkterna i raden för den virtuella datorn. 

1. Välj **Redigera**.

1. Under **ÅTKOMSTKONFIGURATION FÖR JIT VM**kan du antingen redigera de befintliga inställningarna för en redan skyddad port eller lägga till en ny anpassad port. 
  ![jit vm-åtkomst](./media/security-center-just-in-time/edit-policy.png)



## <a name="audit-jit-access-activity-in-security-center"></a>Granska JIT-åtkomstaktivitet i Security Center

Du kan få insikter om vm-aktiviteter med hjälp av loggsökning. Så här visar du loggar:

1. Under **Ny vm-åtkomst**väljer du fliken **Konfigurerad.**
2. Under **Virtuella datorer**väljer du en virtuell dator att visa information om genom att klicka på de tre punkterna i raden för den virtuella datorn och välj **Aktivitetslogg** på menyn. **Aktivitetsloggen** öppnas.

   ![Välj aktivitetslogg](./media/security-center-just-in-time/select-activity-log.png)

   **Aktivitetsloggen** innehåller en filtrerad vy över tidigare åtgärder för den virtuella datorn tillsammans med tid, datum och prenumeration.

Du kan ladda ner logginformationen genom att välja **Klicka här för att ladda ner alla objekt som CSV**.

Ändra filtren och klicka på **Använd** för att skapa en sökning och logg.



## <a name="configure-jit-access-from-an-azure-vms-page"></a>Konfigurera JIT-åtkomst från en azure vm-sida<a name="jit-vm"></a>

För din bekvämlighet kan du ansluta till en virtuell dator med JIT direkt från den virtuella datorns sida i Security Center.

### <a name="configure-jit-access-on-a-vm-via-the-azure-vm-page"></a>Konfigurera JIT-åtkomst på en virtuell dator via sidan Azure VM

För att göra det enkelt att distribuera just-in-time-åtkomst över dina virtuella datorer kan du ställa in en virtuell dator så att endast just-in-time-åtkomst direkt från den virtuella datorn.

1. Sök efter och välj **virtuella datorer**i [Azure-portalen](https://ms.portal.azure.com). 
2. Välj den virtuella dator som du vill begränsa till just-in-time-åtkomst.
3. Välj **Konfiguration**på menyn .
4. Under **Just-in-time-åtkomst**väljer du **Aktivera just-in-time**. 

Detta möjliggör just-in-time-åtkomst för den virtuella datorn med hjälp av följande inställningar:

- Windows-servrar:
    - RDP-port 3389
    - Tre timmars maximal tillåten åtkomst
    - Tillåtna käll-IP-adresser är inställt på Alla
- Linux-servrar:
    - SSH-port 22
    - Tre timmars maximal tillåten åtkomst
    - Tillåtna käll-IP-adresser är inställt på Alla
     
Om en virtuell dator redan har just-in-time aktiverat, när du går till dess konfigurationssida kommer du att kunna se att just-in-time är aktiverat och du kan använda länken för att öppna principen i Azure Security Center för att visa och ändra inställningarna.

![jit config i vm](./media/security-center-just-in-time/jit-vm-config.png)

### <a name="request-jit-access-to-a-vm-via-an-azure-vms-page"></a>Begär JIT-åtkomst till en virtuell dator via en Azure VM-sida

I Azure-portalen, när du försöker ansluta till en virtuell dator, Azure kontroller för att se om du har en just-in-time åtkomstprincip konfigurerad på den virtuella datorn.

- Om du har en JIT-princip konfigurerad på den virtuella datorn kan du klicka på **Begär åtkomst** för att bevilja åtkomst i enlighet med jit-principen för den virtuella datorn. 

  >![jit begäran](./media/security-center-just-in-time/jit-request.png)

  Åtkomst begärs med följande standardparametrar:

  - **käll-IP:**"Any" (*) (kan inte ändras)
  - **tidsintervall:** Tre timmar (kan inte ändras) <!--Isn't this set in the policy-->
  - **portnummer** RDP-port 3389 för Windows / port 22 för Linux (kan ändras)

    > [!NOTE]
    > När en begäran har godkänts för en virtuell dator som skyddas av Azure-brandväggen, ger Security Center användaren rätt anslutningsinformation (portmappningen från DNAT-tabellen) som ska användas för att ansluta till den virtuella datorn.

- Om du inte har JIT konfigurerat på en virtuell dator uppmanas du att konfigurera en JIT-princip på den.

  ![jit fråga](./media/security-center-just-in-time/jit-prompt.png)

## <a name="configure-a-jit-policy-on-a-vm-programmatically"></a>Konfigurera en JIT-princip på en virtuell dator programmässigt<a name="jit-program"></a>

Du kan ställa in och använda just-in-time via REST API:er och via PowerShell.

### <a name="jit-vm-access-via-rest-apis"></a>ÅTKOMST TILL JIT VM via REST-API:er

Åtkomstfunktionen för just-in-time-vm kan användas via Azure Security Center API. Du kan få information om konfigurerade virtuella datorer, lägga till nya, begära åtkomst till en virtuell dator med mera via det här API:et. Se [Jit Network Access Policies](https://docs.microsoft.com/rest/api/securitycenter/jitnetworkaccesspolicies)om du vill veta mer om REST-API:et just-in-time.

### <a name="jit-vm-access-via-powershell"></a>ÅTKOMST TILL JIT VM via PowerShell

Om du vill använda just-in-time VM-åtkomstlösningen via PowerShell använder du de `Set-AzJitNetworkAccessPolicy`officiella Azure Security Center PowerShell-cmdletsna och specifikt .

I följande exempel anges en åtkomstprincip för just-in-time-vm på en viss virtuell dator och följande anges:

1.  Stäng portarna 22 och 3389.

2.  Ange ett maximalt tidsfönster på 3 timmar för varje så att de kan öppnas per godkänd begäran.
3.  Tillåter den användare som begär åtkomst för att styra källans IP-adresser och tillåter användaren att upprätta en lyckad session på en godkänd just-in-time-åtkomstbegäran.

Kör följande i PowerShell för att åstadkomma detta:

1.  Tilldela en variabel som innehåller åtkomstprincipen för ny virtuell dator för en virtuell dator:

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

2.  Infoga åtkomstprincipen för virtuell dator med bara i tid i en matris:
    
        $JitPolicyArr=@($JitPolicy)

3.  Konfigurera åtkomstprincipen för just-in-time VM på den valda virtuella datorn:
    
        Set-AzJitNetworkAccessPolicy -Kind "Basic" -Location "LOCATION" -Name "default" -ResourceGroupName "RESOURCEGROUP" -VirtualMachine $JitPolicyArr 

### <a name="request-access-to-a-vm-via-powershell"></a>Begär åtkomst till en virtuell dator via PowerShell

I följande exempel kan du se en ny virtuell datoråtkomstbegäran till en viss virtuell dator där port 22 begärs öppnas för en viss IP-adress och under en viss tid:

Kör följande i PowerShell:
1.  Konfigurera åtkomstegenskaperna för vm-begäran

        $JitPolicyVm1 = (@{
          id="/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME"
        ports=(@{
           number=22;
           endTimeUtc="2018-09-17T17:00:00.3658798Z";
           allowedSourceAddressPrefix=@("IPV4ADDRESS")})})
2.  Infoga parametrarna för åtkomstbegäran för virtuella datorer i en matris:

        $JitPolicyArr=@($JitPolicyVm1)
3.  Skicka åtkomsten för begäran (använd det resurs-ID du fick i steg 1)

        Start-AzJitNetworkAccessPolicy -ResourceId "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Security/locations/LOCATION/jitNetworkAccessPolicies/default" -VirtualMachine $JitPolicyArr

Mer information finns i [PowerShell-cmdlet-dokumentationen](https://docs.microsoft.com/powershell/scripting/developer/cmdlet/cmdlet-overview).


## <a name="automatic-cleanup-of-redundant-jit-rules"></a>Automatisk rensning av redundanta JIT-regler 

När du uppdaterar en JIT-princip körs ett rensningsverktyg automatiskt för att kontrollera giltigheten för hela regeluppsättningen. Verktyget söker efter avvikelser mellan regler i din princip och regler i NSG. Om rensningsverktyget hittar en obalans avgör det orsaken och, när det är säkert att göra det, tar bort inbyggda regler som inte behövs längre. Dammsugaren tar aldrig bort regler som du har skapat.

Exempel på scenarier när dammsugaren kan ta bort en inbyggd regel:

- När två regler med identiska definitioner finns och en har högre prioritet än den andra (mening, kommer regeln med lägre prioritet aldrig att användas)
- När en regelbeskrivning innehåller namnet på en virtuell dator som inte matchar mål-IP i regeln 


## <a name="next-steps"></a>Nästa steg

I den här artikeln fick du veta hur just-in-time VM-åtkomst i Security Center hjälper dig att kontrollera åtkomsten till dina virtuella Azure-datorer.

I följande avsnitt kan du lära dig mer om Security Center:

- [Ange säkerhetsprinciper](tutorial-security-policy.md) – Lär dig hur du konfigurerar säkerhetsprinciper för dina Azure-prenumerationer och resursgrupper.
- [Hantera säkerhetsrekommendationer](security-center-recommendations.md) – Lär dig hur rekommendationer hjälper dig att skydda dina Azure-resurser.
- Övervakning av [säkerhetshälsa](security-center-monitoring.md) – Lär dig hur du övervakar hälsotillståndet för dina Azure-resurser.