<!--author=SharS last changed: 12/01/15-->

### <a name="step-1-authorize-a-device-to-change-the-service-data-encryption-key-in-the-azure-classic-portal"></a>Steg 1: Ge en enhet för att ändra krypteringsnyckeln för tjänstdata i den klassiska Azure-portalen
Vanligtvis begär enhetsadministratören att administratören godkänna en enhet för att ändra datakrypteringsnycklar för tjänsten. Tjänstadministratören godkänner sedan enheten för att ändra nyckeln.

Det här steget utförs i den klassiska Azure-portalen. Administratören kan välja en enhet från listan över enheter som är berättigade till ha behörighet. Enheten har sedan behörighet att starta tjänsten data krypteringsprocessen ändringen.

#### <a name="which-devices-can-be-authorized-to-change-service-data-encryption-keys"></a>Vilka enheter kan ha behörighet att ändra tjänsten datakrypteringsnycklar?
En enhet måste uppfylla följande kriterier innan den kan ha behörighet att starta tjänsten kryptering viktiga ändringar:

* Enheten måste vara online för att vara kvalificerad för auktoriseringen av tjänsten data kryptering ändringen.
* Du kan godkänna på samma enhet igen efter 30 minuter om viktiga ändringen inte har initierats.
* Du kan godkänna en annan enhet, under förutsättning att ändringen inte har initierats av tidigare godkänd enheten. När den nya enheten har auktoriserats, kan inte gamla enheten initiera ändringen.
* Du kan inte godkänna en enhet när förnyelsen av datakrypteringsnyckeln för tjänsten pågår.
* Du kan godkänna en enhet när några av de enheter som registrerats för tjänsten har återställts via kryptering medan andra inte har. I sådana fall är berättigade enheter de som har slutfört krypteringsnyckeln för tjänstdata ändra.

> [!NOTE]
> Virtuella StorSimple-enheter visas inte i listan över enheter som kan ha behörighet att starta ändringen i den klassiska Azure-portalen.
> 
> 

Utför följande steg för att välja och auktorisera en enhet för att initiera tjänsten data encryption key ändras.

#### <a name="to-authorize-a-device-to-change-the-key"></a>Att auktorisera en enhet för att ändra nyckeln
1. På sidan service instrumentpanelen **ändra krypteringsnyckel för tjänstdata**.
   
    ![Ändra service krypteringsnyckeln](./media/storsimple-change-data-encryption-key/HCS_ChangeServiceDataEncryptionKey-include.png)
2. I den **ändra krypteringsnyckel för tjänstdata** dialogrutan väljer och auktorisera en enhet för att initiera tjänsten data encryption key ändras. Den nedrullningsbara listan har alla kvalificerade enheter som kan godkännas.
3. Klicka på kryssikonen ![kryssikon](./media/storsimple-change-data-encryption-key/HCS_CheckIcon-include.png).

### <a name="step-2-use-windows-powershell-for-storsimple-to-initiate-the-service-data-encryption-key-change"></a>Steg 2: Använda Windows PowerShell för StorSimple att initiera tjänsten data encryption key ändras
Det här steget utförs i Windows PowerShell för StorSimple-gränssnittet på behöriga StorSimple-enheten.

> [!NOTE]
> Inga åtgärder kan utföras i den klassiska Azure-portalen för din StorSimple Manager-tjänsten tills nyckelförnyelse har slutförts.
> 
> 

Utför följande steg om du använder enhetens seriekonsol för att ansluta till Windows PowerShell-gränssnittet.

#### <a name="to-initiate-the-service-data-encryption-key-change"></a>Att starta tjänsten data encryption key ändras
1. Välj alternativ 1 för att logga in med fullständig åtkomst.
2. Skriv följande vid kommandotolken:
   
     `Invoke-HcsmServiceDataEncryptionKeyChange`
3. När cmdleten har slutförts får du en ny krypteringsnyckel för tjänstdata. Kopiera och spara den här nyckeln för användning i steg 3 i den här processen. Den här nyckeln används för att uppdatera alla enheter som registrerats med StorSimple Manager-tjänsten.
   
   > [!NOTE]
   > Den här processen måste initieras inom fyra timmar för att auktorisera en StorSimple-enhet.
   > 
   > 
   
   Den här nya nyckeln skickas sedan till tjänsten ska kunna skickas till alla enheter som registreras med tjänsten. En avisering visas sedan på instrumentpanelen för tjänsten. Tjänsten kommer att inaktivera alla åtgärder på registrerade enheter och enhetsadministratören sedan måste du uppdatera krypteringsnyckeln för tjänstdata på andra enheter. I/o (värdar skicka data till molnet) kommer inte att avbrytas.
   
   Om du har en enda enhet som registrerats till tjänsten förnyelse processen är slutförd och du kan hoppa över nästa steg. Om du har flera enheter som registrerats till tjänsten kan du gå vidare till steg 3.

### <a name="step-3-update-the-service-data-encryption-key-on-other-storsimple-devices"></a>Steg 3: Uppdatera krypteringsnyckeln för tjänstdata på andra StorSimple-enheter
Dessa steg måste utföras i Windows PowerShell-gränssnittet för din StorSimple-enhet om du har flera enheter som registrerats med StorSimple Manager-tjänsten. Den nyckel som du fick i steg 2: använda Windows PowerShell för StorSimple att initiera tjänsten data encryption key ändringen måste användas för att uppdatera alla återstående StorSimple-enheten registrerad med StorSimple Manager-tjänsten.

Utför följande steg för att uppdatera tjänsten datakryptering på enheten.

#### <a name="to-update-the-service-data-encryption-key"></a>Uppdatera krypteringsnyckeln för tjänstdata
1. Använda Windows PowerShell för StorSimple för att ansluta till konsolen. Välj alternativ 1 för att logga in med fullständig åtkomst.
2. Skriv följande vid kommandotolken:
   
    `Invoke-HcsmServiceDataEncryptionKeyChange – ServiceDataEncryptionKey`
3. Ange den krypteringsnyckel för tjänstdata som du fick i [steg 2: använda Windows PowerShell för StorSimple att initiera tjänsten data encryption key ändras](#to-initiate-the-service-data-encryption-key-change).

