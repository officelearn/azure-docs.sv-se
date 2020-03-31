---
title: Konfigurera en domänoberoende resursgrupp för arbetsgrupp
description: Lär dig hur du konfigurerar en Active Directory Domain-oberoende arbetsgrupp alltid på tillgänglighetsgrupp på en virtuell SQL Server-dator i Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/29/2020
ms.author: mathoma
ms.openlocfilehash: 72c04cf5e3e5fbdeac2d267dfc7b2703bd37a1c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77122680"
---
# <a name="configure-a-workgroup-availability-group"></a>Konfigurera en tillgänglighetsgrupp för arbetsgrupper 

I den här artikeln beskrivs de steg som krävs för att skapa ett Active Directory-domänoberoende kluster med en alltid på tillgänglighetsgrupp. Detta kallas även ett arbetsgruppskluster. Den här artikeln fokuserar på de steg som är relevanta för att förbereda och konfigurera arbetsgruppen och tillgänglighetsgruppen och glosses över steg som beskrivs i andra artiklar, till exempel hur du skapar klustret eller distribuerar tillgänglighetsgruppen. 


## <a name="prerequisites"></a>Krav

Om du vill konfigurera en tillgänglighetsgrupp för arbetsgrupper behöver du följande:
- Minst två virtuella datorer med Windows Server 2016 (eller högre) som kör SQL Server 2016 (eller senare), som distribueras till samma tillgänglighetsuppsättning eller olika tillgänglighetszoner, med hjälp av statiska IP-adresser. 
- Ett lokalt nätverk med minst 4 kostnadsfria IP-adresser i undernätet. 
- Ett konto på varje dator i administratörsgruppen som också har sysadmin-rättigheter i SQL Server. 
- Öppna portar: TCP 1433, TCP 5022, TCP 59999. 

Som referens används följande parametrar i den här artikeln, men kan ändras efter behov: 

| **Namn** | **Parametern** |
| :------ | :---------------------------------- |
| **Nod1**   | AGNode1 (10.0.0.4) |
| **Nod2**   | AGNode2 (10.0.0.5) |
| **Klusternamn** | AGWGAG (10.0.0.6) |
| **Lyssnaren** | AGListener (10.0.0.7) | 
| **DNS-suffix** | ag.wgcluster.example.com | 
| **Namn på arbetsgrupp** | AGWorkgroup (AGWorkgroup) | 
| &nbsp; | &nbsp; |

## <a name="set-dns-suffix"></a>Ange DNS-suffix 

I det här steget konfigurerar du DNS-suffixet för båda servrarna. Till exempel `ag.wgcluster.example.com`. På så sätt kan du använda namnet på det objekt som du vill `AGNode1.ag.wgcluster.example.com`ansluta till som en fullständigt kvalificerad adress i nätverket, till exempel . 

Så här konfigurerar du DNS-suffixet:

1. RDP till din första nod och öppna Serverhanteraren. 
1. Välj **Lokal server** och välj sedan namnet på den virtuella datorn under **Datornamn**. 
1. Välj **Ändra...** under **Om du vill byta namn på den här datorn...**. 
1. Ändra namnet på arbetsgruppens namn så att `AGWORKGROUP`det blir något meningsfullt, till exempel : 

   ![Ändra arbetsgruppsnamn](media/virtual-machines-windows-sql-workgroup-availability-group/1-change-workgroup-name.png)

1. Välj **Mer...** om du vill öppna dialogrutan **DNS-suffix och NetBIOS-datornamn.** 
1. Skriv namnet på DNS-suffixet under **Den här datorns primära DNS-suffix,** till exempel `ag.wgcluster.example.com` och välj sedan **OK:** 

   ![Lägg till DNS-suffix](media/virtual-machines-windows-sql-workgroup-availability-group/2-add-dns-suffix.png)

1. Bekräfta att namnet på den **fullständiga datorn** nu visar DNS-suffixet och välj sedan **OK** för att spara ändringarna: 

   ![Lägg till DNS-suffix](media/virtual-machines-windows-sql-workgroup-availability-group/3-confirm-full-computer-name.png)

1. Starta om servern när du uppmanas att göra det. 
1. Upprepa dessa steg på andra noder som ska användas för tillgänglighetsgruppen. 

## <a name="edit-host-file"></a>Redigera värdfil

Eftersom det inte finns någon active directory finns det inget sätt att autentisera Windows-anslutningar. Tilldela därför förtroende genom att redigera värdfilen med en textredigerare. 

Så här redigerar du värdfilen:

1. RDP till din virtuella dator. 
1. Använd **Utforskaren** för `c:\windows\system32\drivers\etc`att gå till . 
1. Högerklicka på **hosts-filen** och öppna filen med **Anteckningar** (eller någon annan textredigerare).
1. I slutet av filen lägger du till en post för varje nod, tillgänglighetsgruppen och lyssnaren i form av `IP Address, DNS Suffix #comment` följande: 

   ```
   10.0.0.4 AGNode1.ag.wgcluster.example.com #Availability group node
   10.0.0.5 AGNode2.ag.wgcluster.example.com #Availability group node
   10.0.0.6 AGWGAG.ag.wgcluster.example.com #Cluster IP
   10.0.0.7 AGListener.ag.wgcluster.example.com #Listener IP
   ```
 
   ![Lägga till poster för IP-adressen, klustret och lyssnaren i värdfilen](media/virtual-machines-windows-sql-workgroup-availability-group/4-host-file.png)

## <a name="set-permissions"></a>Ställa in behörigheter

Eftersom det inte finns någon Active Directory för att hantera behörigheter måste du manuellt tillåta att ett lokalt administratörskonto som inte är inbyggt kan skapa klustret manuellt. 

Det gör du genom att köra följande PowerShell-cmdlet i en administrativ PowerShell-session på varje nod: 

```PowerShell

new-itemproperty -path HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System -Name LocalAccountTokenFilterPolicy -Value 1
```

## <a name="create-the-failover-cluster"></a>Skapa redundansklustret

I det här steget skapar du redundansklustret. Om du inte känner till dessa steg kan du följa dem från [självstudien för redundanskluster](virtual-machines-windows-portal-sql-create-failover-cluster.md#step-2-configure-the-windows-server-failover-cluster-with-storage-spaces-direct).

Anmärkningsvärda skillnader mellan självstudien och vad som ska göras för ett arbetsgruppskluster:
- Avmarkera **Lagring**och **Lagringsutrymme direkt** när klustervalideringen körs. 
- När du lägger till noderna i klustret lägger du till det fullständigt kvalificerade namnet, till exempel:
   - `AGNode1.ag.wgcluster.example.com`
   - `AGNode2.ag.wgcluster.example.com`
- Avmarkera **Lägg till all kvalificerad lagring i klustret**. 

När klustret har skapats tilldelar du en statisk cluster-IP-adress. Det gör du på följande sätt:

1. Öppna **Redundansklusterhanteraren**på en av noderna, välj klustret, högerklicka på **Namnet: \<ClusterNam>** under **Klusterkärnresurser** och välj sedan **Egenskaper**. 

   ![Starta egenskaper för klusternamnet](media/virtual-machines-windows-sql-workgroup-availability-group/5-launch-cluster-name-properties.png)

1. Markera IP-adressen under **IP-adresser** och välj **Redigera**. 
1. Välj **Använd statisk**, ange klustrets IP-adress och välj sedan **OK:** 

   ![Ange en statisk IP-adress för klustret](media/virtual-machines-windows-sql-workgroup-availability-group/6-provide-static-ip-for-cluster.png)

1. Kontrollera att inställningarna ser korrekta ut och välj sedan **OK** för att spara dem:

   ![Verifiera klusteregenskaper](media/virtual-machines-windows-sql-workgroup-availability-group/7-verify-cluster-properties.png)

## <a name="create-a-cloud-witness"></a>Skapa ett molnvittne 

I det här steget konfigurerar du ett molnresursvittne. Om du inte känner till stegen läser du [självstudien för redundanskluster](virtual-machines-windows-portal-sql-create-failover-cluster.md#create-a-cloud-witness). 

## <a name="enable-availability-group-feature"></a>Aktivera funktionen för tillgänglighetsgrupp 

Aktivera funktionen tillgänglighetsgrupp i det här steget. Om du inte känner till stegen läser du [självstudien för tillgänglighetsgruppen](virtual-machines-windows-portal-sql-availability-group-tutorial.md#enable-availability-groups). 

## <a name="create-keys-and-certificate"></a>Skapa nycklar och certifikat

Skapa i det här steget certifikat som en SQL-inloggning använder på den krypterade slutpunkten. Skapa en mapp på varje nod för att `c:\certs`hålla certifikatsäkerhetskopiorna, till exempel . 

Så här konfigurerar du den första noden: 

1. Öppna **SQL Server Management Studio** och anslut till `AGNode1`din första nod, till exempel . 
1. Öppna ett **nytt frågefönster** och kör följande Transact-SQL-uttryck (T-SQL) efter uppdatering till ett komplext och säkert lösenord:

   ```sql
   USE master;  
   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'PassWOrd123!';  
   GO

   --create a cert from the master key
   USE master;  
   CREATE CERTIFICATE AGNode1Cert   
      WITH SUBJECT = 'AGNode1 Certificate';  
   GO  

   --Backup the cert and transfer it to AGNode2
   BACKUP CERTIFICATE AGNode1Cert TO FILE = 'C:\certs\AGNode1Cert.crt';  
   GO  
   ```

1. Skapa sedan HADR-slutpunkten och använda certifikatet för autentisering genom att köra det här T-SQL-uttrycket (Transact-SQL):

   ```sql
   --CREATE or ALTER the mirroring endpoint
   CREATE ENDPOINT hadr_endpoint  
      STATE = STARTED  
      AS TCP (  
         LISTENER_PORT=5022  
         , LISTENER_IP = ALL  
      )   
      FOR DATABASE_MIRRORING (   
         AUTHENTICATION = CERTIFICATE AGNode1Cert  
         , ENCRYPTION = REQUIRED ALGORITHM AES  
         , ROLE = ALL  
      );  
   GO  
   ```

1. Använd **Utforskaren** för att gå till den filplats där certifikatet finns, till exempel `c:\certs`. 
1. Gör en kopia av certifikatet `AGNode1Cert.crt`manuellt, till exempel från den första noden, och överför det till samma plats på den andra noden. 

Så här konfigurerar du den andra noden: 

1. Anslut till den andra noden med SQL `AGNode2`Server Management **Studio,** till exempel . 
1. I ett nytt **frågefönster** kör du följande Transact-SQL-uttryck (T-SQL) efter uppdatering till ett komplext och säkert lösenord: 

   ```sql
   USE master;  
   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'PassWOrd123!';  
   GO 
   
   --create a cert from the master key
   USE master;  
   CREATE CERTIFICATE AGNode2Cert   
      WITH SUBJECT = 'AGNode2 Certificate';  
   GO  
   --Backup the cert and transfer it to AGNode1
   BACKUP CERTIFICATE AGNode2Cert TO FILE = 'C:\certs\AGNode2Cert.crt';  
   GO
   ```

1. Skapa sedan HADR-slutpunkten och använda certifikatet för autentisering genom att köra det här T-SQL-uttrycket (Transact-SQL):

   ```sql
   --CREATE or ALTER the mirroring endpoint
   CREATE ENDPOINT hadr_endpoint  
      STATE = STARTED  
      AS TCP (  
         LISTENER_PORT=5022  
         , LISTENER_IP = ALL  
      )   
      FOR DATABASE_MIRRORING (   
         AUTHENTICATION = CERTIFICATE AGNode2Cert  
         , ENCRYPTION = REQUIRED ALGORITHM AES  
         , ROLE = ALL  
      );  
   GO  
   ```

1. Använd **Utforskaren** för att gå till den filplats där certifikatet finns, till exempel `c:\certs`. 
1. Gör en kopia av certifikatet `AGNode2Cert.crt`manuellt, till exempel från den andra noden, och överför det till samma plats på den första noden. 

Om det finns några andra noder i klustret upprepar du även dessa steg där och ändrar respektive certifikatnamn. 

## <a name="create-logins"></a>Skapa inloggningar

Certifikatautentisering används för att synkronisera data mellan noder. Om du vill tillåta detta skapar du en inloggning för den andra noden, skapar en användare för inloggningen, skapar ett certifikat för inloggningen för att använda säkerhetskopieringscertifikatet och beviljar sedan anslutning på speglingslutpunkten. 

För att göra det, kör du först följande Transact-SQL (T-SQL) fråga på den första noden, till exempel: `AGNode1` 

```sql
--create a login for the AGNode2
USE master;  
CREATE LOGIN AGNode2_Login WITH PASSWORD = 'PassWord123!';  
GO  

--create a user from the login
CREATE USER AGNode2_User FOR LOGIN AGNode2_Login;  
GO  

--create a certificate that the login uses for authentication
CREATE CERTIFICATE AGNode2Cert  
   AUTHORIZATION AGNode2_User  
   FROM FILE = 'C:\certs\AGNode2Cert.crt'  
GO 

--grant connect for login
GRANT CONNECT ON ENDPOINT::hadr_endpoint TO [AGNode2_login];  
GO
```

Kör sedan följande Transact-SQL-fråga (T-SQL) på den `AGNode2`andra noden, till exempel: 

```sql
--create a login for the AGNode1
USE master;  
CREATE LOGIN AGNode1_Login WITH PASSWORD = 'PassWord123!';  
GO  

--create a user from the login
CREATE USER AGNode1_User FOR LOGIN AGNode1_Login;  
GO  

--create a certificate that the login uses for authentication
CREATE CERTIFICATE AGNode1Cert  
   AUTHORIZATION AGNode1_User  
   FROM FILE = 'C:\certs\AGNode1Cert.crt'  
GO 

--grant connect for login
GRANT CONNECT ON ENDPOINT::hadr_endpoint TO [AGNode1_login];  
GO
```

Om det finns några andra noder i klustret upprepar du även dessa steg där och ändrar respektive certifikat och användarnamn. 

## <a name="configure-availability-group"></a>Konfigurera tillgänglighetsgrupp

I det här steget konfigurerar du din tillgänglighetsgrupp och lägger till databaserna i den. Skapa inte en lyssnare just nu. Om du inte är bekant med stegen läser du [självstudien för tillgänglighetsgruppen](virtual-machines-windows-portal-sql-availability-group-tutorial.md#create-the-availability-group). Var noga med att inleda en redundans och failback för att kontrollera att allt fungerar som det ska vara. 

   > [!NOTE]
   > Om det finns ett fel under synkroniseringsprocessen kan `NT AUTHORITY\SYSTEM` du behöva bevilja sysadmin-rättigheter för att `AGNode1` skapa klusterresurser på den första noden, till exempel tillfälligt. 

## <a name="configure-load-balancer"></a>Konfigurera belastningsutjämnare

I det här sista steget konfigurerar du belastningsutjämnaren med hjälp av [Azure-portalen](virtual-machines-windows-portal-sql-alwayson-int-listener.md) eller [PowerShell](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md)


## <a name="next-steps"></a>Efterföljande moment

Du kan också använda [Az SQL VM CLI](virtual-machines-windows-sql-availability-group-cli.md) för att konfigurera en tillgänglighetsgrupp. 


