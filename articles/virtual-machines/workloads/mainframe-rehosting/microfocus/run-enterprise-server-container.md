---
title: Kör Micro Focus Enterprise Server 4.0 i en Docker-behållare på virtuella Azure-datorer
description: Rehost din IBM z / OS stordator arbetsbelastningar genom att köra Micro Focus Enterprise Server i en Docker-behållare på Azure virtuella datorer.
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: sread
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 30d99c3f4767eb50361f7074c0d508fcf309faca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "61488486"
---
# <a name="run-micro-focus-enterprise-server-40-in-a-docker-container-on-azure"></a>Kör Micro Focus Enterprise Server 4.0 i en Docker-behållare på Azure

Du kan köra Micro Focus Enterprise Server 4.0 i en Docker-behållare på Azure. Den här självstudien visar hur du gör. Den använder Windows CICS (Customer Information Control System) acctdemo demonstration för Enterprise Server.

Docker lägger till portabilitet och isolering till program. Du kan till exempel exportera en Docker-avbildning från en Windows-virtuell dator för att köras på en annan eller från en databas till en Windows-server med Docker. Docker-avbildningen körs på den nya platsen med samma konfiguration – utan att behöva installera Enterprise Server. Det är en del av bilden. Licensöverväganden gäller fortfarande.

Den här självstudien installerar **Windows 2016-datacentret med virtuella behållare** (VM) från Azure Marketplace. Den här virtuella datorn innehåller **Docker 18.09.0**. Stegen nedan visar hur du distribuerar behållaren, kör den och ansluter sedan till den med en 3270-emulator.

## <a name="prerequisites"></a>Krav

Innan du börjar kan du läsa in följande förutsättningar:

- En Azure-prenumeration. Om du inte har ett, skapa ett [gratis konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

- Micro Focus-programvaran och en giltig licens (eller utvärderingslicens). Om du är en befintlig Micro Focus-kund kontaktar du din Micro Focus-representant. Annars [begär du en rättegång](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/).

     > [!NOTE]
     > Docker-demonstrationsfilerna ingår i Enterprise Server 4.0. Den här självstudien använder\_ent server\_dockerfiles\_4.0\_windows.zip. Öppna den från samma plats som du har åtkomst till installationsfilen för Enterprise Server eller gå till *Micro Focus* för att komma igång.

- Dokumentationen för [Enterprise Server och Enterprise Developer](https://www.microfocus.com/documentation/enterprise-developer/#").

## <a name="create-a-vm"></a>Skapa en virtuell dator

1. Skydda mediet från\_\_ent-serverns\_dockerfiles\_4.0 windows.zip-fil. Säkra ES-Docker-Prod-XXXXXXXX.mflic-licensieringsfilen (krävs för att skapa Docker-avbildningarna).

2. Skapa den virtuella datorn. Det gör du genom att öppna Azure-portalen, välja **Skapa en resurs** längst upp till vänster och filtrera efter *Windows-server*. I resultatet väljer du **Windows Server 2016 Datacenter – med behållare**.

     ![Sökresultat för Azure Portal](media/01-portal.png)

3. Om du vill konfigurera egenskaperna för den virtuella datorn väljer du instansinformation:

    1. Välj storlek på den virtuella datorn. För den här självstudien kan du använda en **virtuell standardDS2\_v2-dator** med 2 virtuella processorer och 7 GB minne.

    2. Välj den **region-** och **resursgrupp** som du vill distribuera till.

    3. För **tillgänglighetsalternativ**använder du standardinställningen.

    4. För **Användarnamn**skriver du det administratörskonto som du vill använda och lösenordet.

    5. Kontrollera att **port 3389 RDP** är öppen. Endast den här porten måste vara offentligt exponerad, så att du kan logga in på den virtuella datorn. Acceptera sedan alla standardvärden och klicka på **Granska+ skapa**.

     ![Skapa ett fönster för virtuella datorer](media/container-02.png)

4. Vänta tills distributionen är klar (ett par minuter). Ett meddelande anger att den virtuella datorn har skapats.

5. Klicka på **Gå till resurs** för att gå till **bladet Översikt** för den virtuella datorn.

6. Klicka på knappen **Anslut** till höger. Alternativen **Anslut till virtuella datorer** visas till höger.

7. Klicka på knappen **Hämta RDP-fil** om du vill hämta RDP-filen som gör att du kan ansluta till den virtuella datorn.

8. När filen har hämtats öppnar du den och skriver in användarnamnet och lösenordet som du skapade för den virtuella datorn.

     > [!NOTE]
     > Använd inte företagets autentiseringsuppgifter för att logga in. (RDP-klienten förutsätter att du kanske vill använda dessa. Det gör du inte.)

9.  Välj **Fler alternativ**och välj sedan autentiseringsuppgifter för den virtuella datorn.

Nu körs den virtuella datorn och ansluts via RDP. Du är inloggad och redo för nästa steg.

## <a name="create-a-sandbox-directory-and-upload-the-zip-file"></a>Skapa en sandboxkatalog och ladda upp zip-filen

1.  Skapa en katalog på den virtuella datorn där du kan ladda upp demo- och licensfilerna. Till exempel **\\C: Sandbox**.

2.  Ladda upp **ent\_\_server\_dockerfiles\_4.0 windows.zip** och **ES-Docker-Prod-XXXXXXXX.mflic** fil till katalogen du skapade.

3.  Extrahera innehållet i zip-filen till **ent-servern\_\_\_dockerfiles\_4.0 windows** katalog som skapats av utdragsprocessen. Den här katalogen innehåller en readme-fil (som .html- och .txt-fil) och två underkataloger, **EnterpriseServer** och **Exempel**.

4.  Kopiera **ES-Docker-Prod-XXXXXXXX.mflic** till\\C:\\Sandbox ent\_server\_dockerfiles\_4.0\_windows\\EnterpriseServer och C:\\Sandbox\\ent\_\_server dockerfiles\_4.0\_windows\\Examples\\CICS kataloger.

> [!NOTE]
> Se till att du kopierar licensieringsfilen till båda katalogerna. De krävs för att Docker-byggsteget ska se till att avbildningarna är korrekt licensierade.

## <a name="check-docker-version-and-create-base-image"></a>Kontrollera Docker-versionen och skapa basavbildning

> [!IMPORTANT]
> Att skapa rätt Docker-avbildning är en tvåstegsprocess. Skapa först grundavbildningen Enterprise Server 4.0.Skapa sedan en annan bild för x64-plattformen. Även om du kan skapa en x86 -bild (32 bitars) använder du 64-bitarsbilden.

1. Öppna en kommandotolk.

2. Kontrollera att Docker är installerat. Skriv följande i kommandotolken:

    ```
        docker version
    ```

     Till exempel var versionen 18.09.0 när detta skrevs.

3. Om du vill ändra katalogen skriver **du cd \Sandbox\ent_server_dockerfiles_4.0_windows\EnterpriseServer**.

4. Skriv **bld.bat IacceptEULA** för att påbörja byggprocessen för den första basavbildningen. Vänta några minuter innan den här processen ska köras. I resultatet märker du de två bilder som har skapats – en för x64 och en för x86:

     ![Kommandofönster som visar bilder](media/container-04.png)

5. Om du vill skapa den slutliga avbildningen för CICS-demonstrationen växlar du till CICS-katalogen genom att skriva **cd\\\\Sandbox ent\_server\_dockerfiles\_4.0\_windows\\Examples\\CICS**.

6. Om du vill skapa bilden skriver **du bld.bat x64**. Vänta några minuter innan processen ska köras och meddelandet om att bilden skapades.

7. Skriv **docker-avbildningar** för att visa en lista över alla Docker-avbildningar som är installerade på den virtuella datorn. Se till att **mikrofokus/es-acctdemo** är en av dem.

     ![Kommandofönster som visar es-acctdemo-bild](media/container-05.png)

## <a name="run-the-image"></a>Kör bilden 

1.  Om du vill starta Enterprise Server 4.0 och acctdemo-programmet visas i kommandotolken:

    ```
         docker run -p 16002:86/tcp -p 16002:86/udp -p 9040-9050:9040-9050 -p 9000-9010:9000-9010 -ti --network="nat" --rm microfocus/es-acctdemo:win_4.0_x64
    ```

2.  Installera en 3270 terminal emulator som [x3270](http://x3270.bgp.nu/) och använda den för att ansluta, via port 9040, till bilden som körs.

3.  Hämta IP-adressen för acctdemo-behållaren så att Docker kan fungera som en DHCP-server för de behållare som hanteras:

    1.  Hämta ID:t för behållaren som körs. Skriv **Docker ps** i kommandotolken och notera ID **(22a0fe3159d0** i det här exemplet). Spara den till nästa steg.

    2.  Om du vill hämta IP-adressen för acctdemo-behållaren använder du behållar-ID:et från föregående steg enligt följande:

    ```
       docker inspect <containerID> --format="{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```

       Ett exempel:

    ```   
        docker inspect 22a0fe3159d0 --format="{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```
4. Observera IP-adressen för acctdemo-avbildningen. Adressen i följande utdata är till exempel 172.19.202.52.

     ![Kommandofönster som visar IP-adress](media/container-06.png)

5. Montera bilden med emulatorn. Konfigurera emulatorn så att adressen till acctdemo-avbildningen och port 9040 används. Här är det **172.19.202.52:9040**. Din kommer att vara liknande. Skärmen **Signon till CICS** öppnas.

    ![Signon till CICS-skärm](media/container-07.png)

6. Logga in på CICS-regionen genom att ange **SYSAD** för **USERID** och **SYSAD** för **lösenordet**.

7. Rensa skärmen med hjälp av emulatorns nyckelkarta. För x3270 väljer du **menyalternativet Keymap.**

8. Om du vill starta acctdemo-programmet skriver du **ACCT**. Den första skärmen för programmet visas.

     ![Skärmen Kontodemo](media/container-08.png)

9. Experimentera med visningskontotyper. Skriv till exempel **D** för begäran och **11111** för **KONTOT**. Andra kontonummer att prova är 22222, 33333 och så vidare.

     ![Skärmen Kontodemo](media/container-09.png)

10. Om du vill visa företagsserveradministrationskonsolen går du till kommandotolken och skriver **start http:172.19.202.52:86**

     ![Administration av Företag Server-konsol](media/container-010.png)

Klart! Nu kör du och hanterar ett CICS-program i en Docker-behållare.

## <a name="next-steps"></a>Nästa steg

- [Installera Micro Focus Enterprise Server 4.0 och Enterprise Developer 4.0 på Azure](./set-up-micro-focus-azure.md)
- [Migrering av program från stordatorer](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
