<!--author=alkohli last changed: 9/17/15-->

#### <a name="to-complete-the-minimum-storsimple-device-setup"></a>Slutför den minimala StorSimple-enhetsinställningen
1. Välj enheten och klicka på **Snabbstart**. Klicka på **Slutför enhetsinställningar** för att starta guiden för enhetskonfiguration.
2. I guiden för enhetskonfiguration, i dialogrutan **Grundläggande inställningar**, gör du följande:
   
   1. Ange ett **eget namn** för din enhet. Standard-enhetsnamnet har information som enhetsmodell serienummer. Du kan tilldela ett eget namn på upp till 64 tecken för att hantera din enhet.
   2. Ange **tidszon** baserat på den geografiska plats där enheten ska distribueras. Enheten använder den här tidszonen för alla schemalagda åtgärder.
   3. Under **DNS-inställningar**, anger du en adress för din **sekundära DNS-server**. Om du använder IPv6, fylls fältet i baserat på det IPv6-prefix som ges i Windows PowerShell-gränssnittet. 
      Om den sekundära DNS-servern inte är konfigurerad, kommer du inte att kunna spara din enhetskonfiguration.
   4. Aktivera minst ett nätverk för iSCSI under iSCSI-aktiverade gränssnitt. Minst ett nätverksgränssnitt måste vara moln-aktiverat och ett måste vara iSCSI-aktiverat. DATA 0 är automatiskt moln-aktiverat.
      
      ![Grundläggande inställningar för minimala StorSimple-enhetsinställningar](./media/storsimple-complete-minimum-device-setup-u1/HCS_MinDeviceSetupBasicSettings1-include.png)
3. Klicka på pilikonen. ![StorSimple-pilikonen](./media/storsimple-complete-minimum-device-setup/HCS_ArrowIcon-include.png)
4. I dialogrutan **Nätverksgränssnitt**, anger du den fasta IP-adressen för Styrenhet 0 och 1. **De styrenhets-fästa IP-adresserna måste vara fria IP-adresser i undernätet som är åtkomliga från enhetens IP-adress.** Om DATA 0-gränssnittet har konfigurerats för IPv4, måste de fasta IP-adresserna anges i IPv4-format. Om du angav ett prefix för IPv6-konfigurationen, fylls de fasta IP-adresserna automatiskt in i fälten.

    ![Nätverksgränssnitt för minimala StorSimple-enhetsinställningar](./media/storsimple-complete-minimum-device-setup-u1/HCS_MinDeviceSetupNetworkInterfaces2-include.png)

    De fasta IP-adresserna för styrenheten används för att underhålla uppdateringarna till enheten och därför måste de vara dirigerbara och kunna ansluta till Internet. Du kan kontrollera att dina fasta styrenhets-IP-adresser är dirigerbara genom att använda [Test]-cmdleten [Test-HcsmConnection]. Följande exempel visar att fasta styrenhets-IP-adresser dirigeras till Internet och har åtkomst till Microsoft Update-servrarna. 

     ![Test-HcsmConnection visar dirigerbara IP-adresser](./media/storsimple-complete-minimum-device-setup-u1/Test-HcsmConnectionOutputRegisteredDevice.png)

1. Klicka på kryssikonen ![StorSimple-kryssikon](./media/storsimple-complete-minimum-device-setup/HCS_CheckIcon-include.png).
   Du återgår då till enhetens **Snabbstart**-sida.
   
   > [!NOTE]
   > Du kan ändra alla andra enhetsinställningar när som helst genom att öppna **Konfigurera**-sidan.
   > 
   > 

<!--Link reference-->
[Test]: https://technet.microsoft.com/library/dn715782(v=wps.630).aspx