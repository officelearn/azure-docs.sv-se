---
title: Just-in-Time-åtkomst för virtuella datorer i Azure Security Center | Microsoft Docs
description: Det här dokumentet visar hur just-in-Time VM Access i Azure Security Center hjälper dig att kontrol lera åtkomsten till dina virtuella Azure-datorer.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: cc4e267c6912b8938db1ba5497a27f9c0026bd79
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80887341"
---
# <a name="secure-your-management-ports-with-just-in-time-access"></a>Skydda dina hanterings portar med just-in-Time-åtkomst

Om du är på Security Center standard pris nivån (se [prissättning](/azure/security-center/security-center-pricing)) kan du låsa inkommande trafik till dina virtuella Azure-datorer med åtkomst till virtuella datorer med just-in-Time (JIT). Detta minskar risken för attacker och ger enkel åtkomst till att ansluta till virtuella datorer när det behövs.

> [!NOTE]
> Security Center VM-åtkomst just-in-Time stöder för närvarande endast virtuella datorer som distribueras via Azure Resource Manager. Läs mer om de klassiska distributions modellerna för och Resource Manager i [Azure Resource Manager vs. Classic-distribution](../azure-resource-manager/management/deployment-models.md).

[!INCLUDE [security-center-jit-description](../../includes/security-center-jit-description.md)]

## <a name="configure-jit-on-a-vm"></a>Konfigurera JIT på en virtuell dator

Det finns tre sätt att konfigurera en JIT-princip på en virtuell dator:

- [Konfigurera JIT-åtkomst i Azure Security Center](#jit-asc)
- [Konfigurera JIT-åtkomst på en virtuell Azure-sida](#jit-vm)
- [Konfigurera en JIT-princip på en virtuell dator program mässigt](#jit-program)

## <a name="configure-jit-in-azure-security-center"></a>Konfigurera JIT i Azure Security Center

Från Security Center kan du konfigurera en JIT-princip och begära åtkomst till en virtuell dator med en JIT-princip

### <a name="configure-jit-access-on-a-vm-in-security-center"></a>Konfigurera JIT-åtkomst på en virtuell dator i Security Center<a name="jit-asc"></a>

1. Öppna instrumentpanelen för **Security Center**.

1. I det vänstra fönstret väljer du **VM-åtkomst just-in-Time**.

    ![Just-in-Time-panel för VM-åtkomst](./media/security-center-just-in-time/just-in-time.png)

    **Just-in-Time-** fönstret för VM-åtkomst öppnas och visar information om status för dina virtuella datorer:

    - **Konfigurerade** – virtuella datorer som har kon figurer ATS för att stödja just-in-Time VM-åtkomst. De data som visas är den senaste veckan och omfattar för varje virtuell dator antalet godkända begär Anden, senaste åtkomst datum och tid och senaste användare.
    - **Rekommenderade** -virtuella datorer som har stöd för just-in-Time VM-åtkomst men inte har kon figurer ATS för. Vi rekommenderar att du aktiverar just-in-Time-åtkomst kontroll för de här virtuella DATORerna.
    - **Ingen rekommendation** – Orsaker som kan orsaka att en virtuell dator inte rekommenderas är:
      - Saknar NSG – just-in-Time-lösningen kräver att en NSG är på plats.
      - Klassisk VM-Security Center just-in-Time-åtkomst för virtuella datorer stöder för närvarande endast virtuella datorer som distribueras via Azure Resource Manager. En klassisk distribution stöds inte av just-in-Time-lösningen. 
      - Övrigt – en virtuell dator finns i den här kategorin om just-in-Time-lösningen är inaktive rad i säkerhets principen för prenumerationen eller resurs gruppen, eller om den virtuella datorn saknar en offentlig IP-adress och inte har en NSG på plats.

1. Välj fliken **rekommenderas** .

1. Under **virtuell dator**klickar du på de virtuella datorer som du vill aktivera. Detta sätter en bock bredvid en virtuell dator.

      ![Aktivera just-in-Time-åtkomst](./media/security-center-just-in-time/enable-just-in-time.png)

1. Klicka på **Aktivera JIT på virtuella datorer**. Ett fönster visas som visar standard portarna som rekommenderas av Azure Security Center:
    - 22 – SSH
    - 3389 – RDP
    - 5985 – WinRM 
    - 5986 – WinRM
1. Du kan också lägga till anpassade portar i listan:

      1. Klicka på **Lägg till**. Fönstret **Lägg till port konfiguration** öppnas.
      1. För varje port som du väljer att konfigurera, både standard och anpassad, kan du anpassa följande inställningar:
            - **Protokoll typ**– det protokoll som tillåts på den här porten när en begäran godkänns.
            - **Tillåtna käll-IP-adresser**– de IP-intervall som tillåts på den här porten när en begäran har godkänts.
            - **Maximal begär ande tid**– maximalt tids period då en bestämd port kan öppnas.

     1. Klicka på **OK**.

1. Klicka på **Spara**.

> [!NOTE]
>När JIT VM-åtkomst är aktive rad för en virtuell dator skapar Azure Security Center "neka all inkommande trafik" för de valda portarna i de nätverks säkerhets grupper som är kopplade till och Azure-brandväggen med den. Om andra regler har skapats för de valda portarna, prioriteras de befintliga reglerna för den nya regeln "neka all inkommande trafik". Om det inte finns några befintliga regler på de valda portarna, har den nya regeln "neka all inkommande trafik" företräde överst i nätverks säkerhets grupperna och Azure-brandväggen.


## <a name="request-jit-access-via-security-center"></a>Begär JIT-åtkomst via Security Center

Så här begär du åtkomst till en virtuell dator via Security Center:

1. Under **just-in-Time VM-åtkomst**väljer du fliken **konfigurerad** .

1. Under **virtuell dator**klickar du på de virtuella datorer som du vill begära åtkomst för. Detta sätter en bock bredvid den virtuella datorn.

    - Ikonen i kolumnen **anslutnings information** visar om JIT är aktiverat på NSG eller VB. Om det är aktiverat på båda visas bara brand Väggs ikonen.

    - Kolumnen **anslutnings information** innehåller den information som krävs för att ansluta den virtuella datorn och dess öppna portar.

      ![Begära just-in-time-åtkomst](./media/security-center-just-in-time/request-just-in-time-access.png)

1. Klicka på **begär åtkomst**. Fönstret **begär åtkomst** öppnas.

      ![JIT-information](./media/security-center-just-in-time/just-in-time-details.png)

1. Under **begär åtkomst**, för varje virtuell dator, konfigurerar du de portar som du vill öppna och käll-IP-adresserna som porten är öppen på och tids perioden som porten ska vara öppen för. Det går bara att begära åtkomst till de portar som kon figurer ATS i just-in-time-principen. Varje port har en högsta tillåten tid som härleds från just-in-time-principen.

1. Klicka på **öppna portar**.

> [!NOTE]
> Om en användare som begär åtkomst ligger bakom en proxyserver kanske inte alternativet **min IP** fungerar. Du kan behöva definiera det fullständiga IP-adressintervallet för organisationen.



## <a name="edit-a-jit-access-policy-via-security-center"></a>Redigera en JIT-fjärråtkomstprincip via Security Center

Du kan ändra en VM: s befintliga princip för just-in-Time genom att lägga till och konfigurera en ny port för att skydda den virtuella datorn, eller genom att ändra någon annan inställning som är relaterad till en redan skyddad port.

Så här redigerar du en befintlig just-in-Time-princip för en virtuell dator:

1. På fliken **konfigurerad** under **virtuella datorer**väljer du en virtuell dator som du vill lägga till en port genom att klicka på de tre punkterna i raden för den virtuella datorn. 

1. Välj **Redigera**.

1. Under **konfiguration av JIT VM-åtkomst**kan du antingen redigera befintliga inställningar för en redan skyddad port eller lägga till en ny anpassad port. 
  ![JIT VM-åtkomst](./media/security-center-just-in-time/edit-policy.png)



## <a name="audit-jit-access-activity-in-security-center"></a>Granska JIT Access-aktivitet i Security Center

Du kan få insikter om VM-aktiviteter med loggs ökning. Så här visar du loggar:

1. Under **just-in-Time VM-åtkomst**väljer du fliken **konfigurerad** .
2. Under **virtuella datorer**väljer du en virtuell dator för att visa information om genom att klicka på de tre punkterna i raden för den virtuella datorn och välj **aktivitets logg** på menyn. **Aktivitets loggen** öppnas.

   ![Välj aktivitets logg](./media/security-center-just-in-time/select-activity-log.png)

   **Aktivitets loggen** innehåller en filtrerad vy över tidigare åtgärder för den virtuella datorn tillsammans med tid, datum och prenumeration.

Du kan hämta logg informationen genom att välja **Klicka här för att ladda ned alla objekt som CSV**.

Ändra filtren och klicka på **Använd** för att skapa en sökning och logg.



## <a name="configure-jit-access-from-an-azure-vms-page"></a>Konfigurera JIT-åtkomst från en Azure VM-sida<a name="jit-vm"></a>

För din bekvämlighet kan du ansluta till en virtuell dator med JIT direkt från den virtuella datorns sida i Security Center.

### <a name="configure-jit-access-on-a-vm-via-the-azure-vm-page"></a>Konfigurera JIT-åtkomst på en virtuell dator via sidan virtuell Azure-dator

För att göra det enkelt att distribuera just-in-Time-åtkomst över dina virtuella datorer kan du ställa in en virtuell dator så att endast just-in-Time-åtkomst direkt inifrån den virtuella datorn.

1. Sök efter och välj **virtuella datorer**från [Azure Portal](https://ms.portal.azure.com). 
2. Välj den virtuella dator som du vill begränsa till just-in-Time-åtkomst.
3. I menyn väljer du **konfiguration**.
4. Under **just-in-Time-åtkomst**väljer du **Aktivera just-in-Time**. 

Detta möjliggör just-in-Time-åtkomst för den virtuella datorn med följande inställningar:

- Windows-servrar:
    - RDP-port 3389
    - Tre timmar med högsta tillåtna åtkomst
    - Tillåtna käll-IP-adresser har angetts till valfri
- Linux-servrar:
    - SSH-port 22
    - Tre timmar med högsta tillåtna åtkomst
    - Tillåtna käll-IP-adresser har angetts till valfri
     
Om en virtuell dator redan har aktiverat just-in-Time, kan du se att just-in-Time är aktiverat när du går till konfigurations sidan och du kan använda länken för att öppna principen i Azure Security Center för att visa och ändra inställningarna.

![JIT-konfiguration i virtuell dator](./media/security-center-just-in-time/jit-vm-config.png)

### <a name="request-jit-access-to-a-vm-via-an-azure-vms-page"></a>Begär JIT-åtkomst till en virtuell dator via en Azure VM-sida

När du försöker ansluta till en virtuell dator i Azure Portal, kontrollerar Azure om du har en princip för just-in-Time-åtkomst som kon figurer ATS på den virtuella datorn.

- Om du har en JIT-princip som kon figurer ATS på den virtuella datorn kan du klicka på **begär åtkomst** för att bevilja åtkomst i enlighet med den inaktiverade JIT-principen för den virtuella datorn. 

  >![JIT-begäran](./media/security-center-just-in-time/jit-request.png)

  Åtkomst begärs med följande standard parametrar:

  - **käll-IP**: "any" (*) (kan inte ändras)
  - **tidsintervall**: tre timmar (kan inte ändras) <!--Isn't this set in the policy-->
  - **port nummer** RDP-port 3389 för Windows/port 22 för Linux (kan ändras)

    > [!NOTE]
    > När en begäran har godkänts för en virtuell dator som skyddas av Azure-brandväggen ger Security Center användaren rätt anslutnings information (port mappningen från tabellen DNAT) som ska användas för att ansluta till den virtuella datorn.

- Om du inte har JIT-konfigurerad på en virtuell dator uppmanas du att konfigurera en JIT-princip på den.

  ![JIT-prompt](./media/security-center-just-in-time/jit-prompt.png)

## <a name="configure-a-jit-policy-on-a-vm-programmatically"></a>Konfigurera en JIT-princip på en virtuell dator program mässigt<a name="jit-program"></a>

Du kan konfigurera och använda just-in-Time via REST-API: er och via PowerShell.

### <a name="jit-vm-access-via-rest-apis"></a>JIT VM-åtkomst via REST-API: er

Just-in-Time-funktionen för VM-åtkomst kan användas via Azure Security Center-API: et. Du kan få information om konfigurerade virtuella datorer, lägga till nya, begära åtkomst till en virtuell dator, med mera, via det här API: et. Mer information om just-in-Time-REST API finns i principer för att [få åtkomst till JIT-nätverk](https://docs.microsoft.com/rest/api/securitycenter/jitnetworkaccesspolicies).

### <a name="jit-vm-access-via-powershell"></a>JIT VM-åtkomst via PowerShell

Om du vill använda den just-in-Time-baserade VM-lösningen via PowerShell använder du de officiella Azure Security Center PowerShell- `Set-AzJitNetworkAccessPolicy`cmdletarna och särskilt.

I följande exempel anges en just-in-Time-princip för VM-åtkomst på en enskild virtuell dator och följande anges:

1.    Stäng portarna 22 och 3389.

2.    Ange ett maximalt tidsintervall på 3 timmar för varje så att de kan öppnas per godkänd begäran.
3.    Tillåter den användare som begär åtkomst att kontrol lera käll-IP-adresserna och gör det möjligt för användaren att upprätta en lyckad session vid en godkänd just-in-Time-åtkomstbegäran.

Kör följande i PowerShell för att göra detta:

1.    Tilldela en variabel som innehåller just-in-Time-åtkomst princip för virtuell dator för en virtuell dator:

        $JitPolicy = (@ {ID = "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME" Ports = (@ {Number = 22;        protokoll = "\*";        allowedSourceAddressPrefix = @ ("\*");        maxRequestAccessDuration = "PT3H"}, @ {Number = 3389;        protokoll = "\*";        allowedSourceAddressPrefix = @ ("\*");        maxRequestAccessDuration = "PT3H"})})

2.    Infoga VM-principen för just-in-Time-VM i en matris:
    
        $JitPolicyArr = @ ($JitPolicy)

3.    Konfigurera den just-in-Time-princip för VM-åtkomst på den valda virtuella datorn:
    
        Set-AzJitNetworkAccessPolicy – "grundläggande"-plats "plats"-name "default"-ResourceGroupName "RESOURCEGROUP"-VirtualMachine $JitPolicyArr 

### <a name="request-access-to-a-vm-via-powershell"></a>Begär åtkomst till en virtuell dator via PowerShell

I följande exempel kan du se en just-in-Time-begäran om VM-åtkomst till en angiven virtuell dator där port 22 begärs att öppnas för en speciell IP-adress och under en angiven tids period:

Kör följande i PowerShell:
1.    Konfigurera åtkomst egenskaper för VM-begäran

        $JitPolicyVm 1 = (@ {ID = "/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME" Ports = (@ {Number = 22;      endTimeUtc = "2018-09-17T17:00:00.3658798 Z";      allowedSourceAddressPrefix = @ ("IPV4ADDRESS")})})
2.    Infoga parametrarna för VM-åtkomstbegäran i en matris:

        $JitPolicyArr = @ ($JitPolicyVm 1)
3.    Skicka begär ande åtkomst (Använd det resurs-ID som du fick i steg 1)

        Start-AzJitNetworkAccessPolicy-ResourceId "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Security/locations/LOCATION/jitNetworkAccessPolicies/default"-VirtualMachine $JitPolicyArr

Mer information finns i PowerShell- [cmdlet-dokumentationen](https://docs.microsoft.com/powershell/scripting/developer/cmdlet/cmdlet-overview).


## <a name="automatic-cleanup-of-redundant-jit-rules"></a>Automatisk rensning av redundanta JIT-regler 

När du uppdaterar en JIT-princip körs automatiskt ett rensnings verktyg för att kontrol lera giltigheten för hela ruleset. Verktyget söker efter avvikelser mellan regler i principen och reglerna i NSG. Om ett matchnings fel upptäcks i rensnings verktyget, bestäms orsaken och när det är säkert att göra det, tar bort inbyggda regler som inte behövs längre. Rengöringen tar aldrig bort regler som du har skapat.

Exempel scenarier när rengörings kassetten kan ta bort en inbyggd regel:

- När två regler med identiska definitioner finns och en har högre prioritet än den andra (vilket innebär att regeln med lägre prioritet aldrig används)
- När en regel Beskrivning innehåller namnet på en virtuell dator som inte matchar mål-IP-adressen i regeln 


## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur just-in-Time VM Access i Security Center hjälper dig att styra åtkomsten till dina virtuella Azure-datorer.

I följande avsnitt kan du lära dig mer om Security Center:

- Microsoft Learn-modulen [skyddar dina servrar och virtuella datorer från angrepp och angrepp mot skadlig kod med Azure Security Center](https://docs.microsoft.com/learn/modules/secure-vms-with-azure-security-center/)
- [Ange säkerhets principer](tutorial-security-policy.md) – lär dig hur du konfigurerar säkerhets principer för dina Azure-prenumerationer och resurs grupper.
- [Hantera säkerhets rekommendationer](security-center-recommendations.md) – lär dig hur rekommendationer hjälper dig att skydda dina Azure-resurser.
- [Övervakning av säkerhets hälsa](security-center-monitoring.md) – lär dig hur du övervakar hälso tillståndet för dina Azure-resurser.
