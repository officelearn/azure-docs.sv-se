---
title: Konfigurera programåterställning för virtuella Azure-datorer till en sekundär Azure-region med Azure Site Recovery (förhandsversion)
description: Lär dig konfigurera programåterställning för virtuella Azure-datorer till annan Azure-region med Azure Site Recovery-tjänsten.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/01/2017
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: f0b5eac1ac6b682de3ae9e18fd53e8bf55acdde4
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2018
ms.locfileid: "29677429"
---
# <a name="set-up-disaster-recovery-for-azure-vms-to-a-secondary-azure-region-preview"></a>Konfigurera programåterställning för virtuella Azure-datorer till en sekundär Azure-region (förhandsversion)

[Azure Site Recovery](../site-recovery-overview.md)-tjänsten bidrar till din strategi för haveriberedskap genom att hantera och samordna replikering, redundans och återställning av fysiska servrar och virtuella Azure-datorer.

Den här självstudien visar hur du konfigurerar haveriberedskap till en sekundär Azure-region för virtuella Azure-datorer. I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Skapa ett Recovery Services-valv
> * Verifiera målresursuppsättningar
> * Konfigurera utgående åtkomst för virtuella datorer
> * Aktivera replikering för en virtuell dator

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

- Vara säker på att du förstår [arkitekturen och komponenterna för scenariot](concepts-azure-to-azure-architecture.md).
- Granska [kraven för stöd](site-recovery-support-matrix-azure-to-azure.md) för alla komponenter.

## <a name="create-a-vault"></a>Skapa ett valv

Skapa valvet i valfri region, utom i källregionen.

1. Logga in på [Azure-portalen](https://portal.azure.com) > **Recovery Services**.
2. Klicka på **Skapa en resurs** > **Övervakning och hantering** > **Backup och Site Recovery**.
3. I **Namn** anger du ett eget namn som identifierar valvet. Om du har mer än en prenumeration väljer du den lämpligaste.
4. Skapa en resursgrupp eller välj en befintlig. Ange en Azure-region. Information om vilka regioner som stöds finns under Geografisk tillgänglighet i avsnittet med [Azure Site Recovery-prisinformation](https://azure.microsoft.com/pricing/details/site-recovery/).
5. För att snabbt komma åt valvet från instrumentpanelen klickar du på **Fäst på instrumentpanelen** och sedan på **Skapa**.

   ![Nytt valv](./media/azure-to-azure-tutorial-enable-replication/new-vault-settings.png)

   Det nya valvet läggs till på **Instrumentpanelen** under **Alla resurser** och på huvudsidan för **Recovery Services-valv**.

## <a name="verify-target-resources"></a>Verifiera målresurser

1. Verifiera att Azure-prenumerationen låter dig skapa virtuella datorer i målregionen för haveriberedskap. Kontakta supporten och aktivera den kvot som krävs.

2. Se till att din prenumeration har tillräckligt med resurser för att hantera virtuella datorer med de storlekar som de virtuella källdatorerna har. Site Recovery väljer samma storlek eller närmaste möjliga storlek för den virtuella måldatorn.

## <a name="configure-outbound-network-connectivity"></a>Konfigurera utgående nätverksanslutning

Site Recovery kräver att vissa ändringar görs i utgående nätverksanslutning, från virtuella datorer du vill replikera.

- Site Recovery har inte stöd för en autentiseringsproxy för att styra nätverksanslutningar.
- Om du har en autentiseringsproxy kan replikering inte aktiveras.

### <a name="outbound-connectivity-for-urls"></a>Utgående anslutning för webbadresser

Om du använder en webbadressbaserad brandväggsproxy för att styra utgående nätverksanslutningar ger du tillgång till följande webbadresser som används av Site Recovery.

| **URL** | **Detaljer** |
| ------- | ----------- |
| *.blob.core.windows.net | Gör att data kan skrivas från den virtuella datorn till cachelagringskontot i källregionen. |
| login.microsoftonline.com | Tillhandahåller auktorisering och autentisering för Site Recovery-tjänstens webbadresser. |
| *.hypervrecoverymanager.windowsazure.com | Låter den virtuella datorn kommunicera med Site Recovery-tjänsten. |
| *.servicebus.windows.net | Låter den virtuella datorn skriva övervaknings- och diagnostikdata för Site Recovery. |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>Utgående anslutning för IP-adressintervall

När en IP-baserad brandvägg, proxy eller NSG-regler används för att styra utgående anslutningar måste följande IP-adressintervall tillåtas. Ladda ned en lista med intervall med följande länkar:

  - [Microsoft Azure Datacenter IP-intervall](http://www.microsoft.com/download/details.aspx?id=41653)
  - [Windows Azure Datacenter IP-intervall i Tyskland](http://www.microsoft.com/download/details.aspx?id=54770)
  - [Windows Azure Datacenter IP-intervall i Kina](http://www.microsoft.com/download/details.aspx?id=42064)
  - [URL:er och IP-adressintervall för Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity)
  - [IP-adresser för Site Recovery-tjänstens slutpunkter](https://aka.ms/site-recovery-public-ips)

Använd dessa listor och konfigurera nätverksåtkomsten. Du kan använda det här [skriptet](https://gallery.technet.microsoft.com/Azure-Recovery-script-to-0c950702) om du vill skapa de NSG-regler som krävs.

## <a name="verify-azure-vm-certificates"></a>Verifiera certifikat för virtuella Azure-datorer

Kontrollera att alla de senaste rotcertifikaten finns på de virtuella datorer med Windows eller Linux som du vill replikera. Om de senaste rotcertifikaten inte finns kan de virtuella datorerna inte registreras i Site Recovery på grund av säkerhetsbegränsningar.

- I virtuella datorer med Windows installerar du alla de senaste uppdateringarna så att alla betrodda rotcertifikat finns på datorn. I en frånkopplad miljö använder du organisationens vanliga processer för Windows Update och certifikatuppdatering.

- I virtuella datorer med Linux följer du de riktlinjer du fått från Linux-distributören för att hämta de senaste betrodda rotcertifikaten och listan över återkallade certifikat till den virtuella datorn.

## <a name="set-permissions-on-the-account"></a>Ange behörigheter för kontot

Azure Site Recovery har tre inbyggda roller som styr Site Recovery-hanteringen.

- **Site Recovery-bidragsgivare** – Den här rollen har alla behörigheter som krävs för att hantera Azure Site Recovery-åtgärder i ett Recovery Services-valv. En användare med denna roll kan dock inte skapa eller ta bort ett Recovery Services-valv eller tilldela behörighet till andra användare. Den här rollen lämpar sig bäst för haveriberedskapsadministratörer som kan aktivera och hantera haveriberedskap för program eller hela organisationer.

- **Site Recovery-operatör** – Den här rollen har behörighet att utföra och hantera operationer för redundansväxling och återställning av fel. En användare med den här rollen kan inte aktivera eller inaktivera replikering, skapa eller ta bort valv, registrera ny infrastruktur eller tilldela åtkomstbehörigheter till andra användare. Den här rollen lämpar sig bäst för en haveriberedskapsoperatör som kan redundansväxla virtuella datorer eller program efter instruktioner från programägare och IT-administratörer. När problemet lösts kan haveriberedskapsoperatören återaktivera skydd och återställa de virtuella datorerna efter fel.

- **Site Recovery-läsare** – Den här rollen har behörighet att visa all Site Recovery-hantering. Den här rollen lämpar sig bäst för en IT-chef som kan övervaka aktuell skyddsnivå och skapa supportärenden.

Läs mer om [Azure RBAC inbyggda roller](../../active-directory/role-based-access-built-in-roles.md)

## <a name="enable-replication"></a>Aktivera replikering

### <a name="select-the-source"></a>Välj källan

1. I Recovery Services-valven klickar du på valvnamnet > **+Replikera**.
2. I **Källa** väljer du **Azure – FÖRHANDSVERSION**.
3. I **Källplats** väljer du den Azure källregion där de virtuella datorerna körs just nu.
4. Välj **Distributionsmodell för Azure virtuella datorer** för virtuella datorer: **Resurshanterare** eller **Klassisk**.
5. Välj **Källresursgrupp** för resurshanterar-VM eller **molntjänst** för klassiska VM.
6. Spara inställningarna genom att klicka på **OK**.

### <a name="select-the-vms"></a>Välj virtuella datorer

Site Recovery hämtar en lista med de virtuella datorer som är kopplade till prenumerationen och resursgruppen eller molntjänsten.

1. I **Virtuella datorer** väljer du de virtuella datorer du vill replikera.
2. Klicka på **OK**.

### <a name="configure-replication-settings"></a>Konfigurera replikeringsinställningar

Site Recovery skapar standardinställningar och replikeringsprinciper för målregionen. Du kan ändra inställningarna efter behov.

1. Klicka på **inställningar** att visa inställningar för målet.
2. Om du vill åsidosätta standardinställningarna för mål klickar du på **anpassa**. 

![Konfigurera inställningar](./media/azure-to-azure-tutorial-enable-replication/settings.png)


- **Målplats**: den målregion som används för haveriberedskap. Vi rekommenderar att målplatsen överensstämmer med Site Recovery-valvets plats.

- **Målresursgrupp**: den resursgrupp i målregionen som innehåller virtuella Azure-datorer efter redundansväxling. Som standard skapar Site Recovery en ny resursgrupp i målregionen med suffixet ”asr”.

- **Virtuellt målnätverk**: nätverket i målregionen som innehåller virtuella Azure-datorer efter redundansväxling.
  Som standard skapar Site Recovery ett nytt virtuellt nätverk (och undernät) i målregionen med suffixet ”asr”.

- **Cachelagringskonton**: Site Recovery använder ett lagringskonto i källregionen. Ändringar i virtuella källdatorer skickas till det här kontot innan replikering till målplatsen.

- **Mållagringskonton**: Som standard skapar Site Recovery ett nytt lagringskonto i målregionen som speglar lagringskontot för den virtuella källdatorn.

- **Tillgänglighetsuppsättningar för mål**: Som standard skapar Site Recovery en ny tillgänglighetsuppsättning i målregionen med suffixet ”asr”. Det går bara att lägga till tillgänglighetsuppsättningar om virtuella datorer ingår i en tillgänglighetsuppsättning i källregionen.

- **Namn på replikeringsprincip**: Principnamn.

- **Kvarhållning av återställningspunkt**: Som standard behåller Site Recovery återställningspunkter under 24 timmar. Du kan ställa in ett värde mellan 1 och 72 timmar.

- **Appkompatibel ögonblicksbildsfrekvens**: Som standard tar Site Recovery en appkompatibel ögonblicksbild var fjärde timme. Du kan ställa in ett värde mellan 1 och 12 timmar. En appkompatibel ögonblicksbild är en ögonblicksbild av programdata i den virtuella datorn. Volume Shadow Copy-tjänsten (VSS) säkerställer att programmen i den virtuella datorn är i ett konsekvent tillstånd när ögonblicksbilden tas.

### <a name="track-replication-status"></a>Spåra replikeringsstatus

1. I **Inställningar** klickar du på **Uppdatera** för att hämta senaste status.

2. Du kan följa förloppet för jobbet **Aktivera skydd** i **Inställningar** > **Jobb** > **Site Recovery-jobb**.

3. I **inställningar** > **Replikerade objekt** visas status för virtuella datorer och den inledande replikeringsprocessen. Klicka på en virtuell dator för att visa dess inställningar.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen konfigurerats katastrofåterställning för en Azure VM. Nästa steg är att prova konfigurationen.

> [!div class="nextstepaction"]
> [Köra ett återställningstest](azure-to-azure-tutorial-dr-drill.md)
