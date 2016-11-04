<!--author=alkohli last changed: 9/17/15-->

#### Slutför den minimala StorSimple-enhetsinställningen
1. På sidan **Enheter**, väljer du enheten, klicka på pilen mot enhetsnamnet för att gå till den specifika enhetens sida. 
   
    ![Enhetssida med enhet online](./media/storsimple-complete-minimum-device-setup/HCS_DevicesPageM-include.png) 
2. Klicka på snabbstartsikonen ![Snabbstart-ikon](./media/storsimple-complete-minimum-device-setup/HCS_QuickStartIcon-include.png) för att komma åt enhetens snabbstartsida. Klicka på **Slutför enhetsinställningar** för att starta guiden **Enhetskonfiguration**.
   
    ![Snabbstartsida för enheten](./media/storsimple-complete-minimum-device-setup/Device_Quick_Start_page_1M.png)
3. På sidan **Grundläggande inställningar**, gör du följande:
   
   1. Ange ett **eget namn** för din enhet. Standard-enhetsnamnet har information som enhetsmodell serienummer. Du kan tilldela ett eget namn på upp till 64 tecken för att hantera din enhet.
   2. Ange **tidszon** baserat på den geografiska plats där enheten ska distribueras. Enheten använder den här tidszonen för alla schemalagda åtgärder.
   3. Under **DNS-inställningar**, anger du en adress för din **sekundära DNS-server**. Om du använder IPv6, fylls fältet i baserat på det IPv6-prefix som ges i Windows PowerShell-gränssnittet. 
      Om den sekundära DNS-servern inte är konfigurerad, kommer du inte att kunna spara din enhetskonfiguration.
   4. Aktivera minst ett nätverk för iSCSI under iSCSI-aktiverade gränssnitt. Minst ett nätverksgränssnitt måste vara moln-aktiverat och ett måste vara iSCSI-aktiverat. DATA 0 är automatiskt moln-aktiverat.
      
      ![Grundläggande inställningar för minimala StorSimple-enhetsinställningar](./media/storsimple-complete-minimum-device-setup/HCS_MinDeviceSetupBasicSettings1-include.png)
4. Klicka på pilikonen. ![StorSimple-pilikonen](./media/storsimple-complete-minimum-device-setup/HCS_ArrowIcon-include.png)
5. På sidan **Nätverksgränssnitt**, anger du den fasta IP-adressen för Styrenhet 0 och 1. Om DATA 0-gränssnittet har konfigurerats för IPv4, måste de fasta IP-adresserna anges i IPv4-format. Om du angav ett prefix för IPv6-konfigurationen, fylls de fasta IP-adresserna automatiskt in i fälten.

    > [AZURE.NOTE] 
    > 
    > - De styrenhets-fästa IP-adresserna måste vara fria IP-adresser i undernätet som är åtkomliga från enhetens IP-adress.
    > - De fasta IP-adresserna för styrenheten används för att underhålla uppdateringarna till enheten och därför måste de vara dirigerbara och kunna ansluta till Internet.

    ![Nätverksgränssnitt för minimala StorSimple-enhetsinställningar](./media/storsimple-complete-minimum-device-setup/HCS_MinDeviceSetupNetworkInterfaces2-include.png)

1. Klicka på kryssikonen ![StorSimple-kryssikon](./media/storsimple-complete-minimum-device-setup/HCS_CheckIcon-include.png).
   Du återgår då till enhetens **Snabbstart**-sida.
   
   > [!NOTE]
   > Du kan ändra alla andra enhetsinställningar när som helst genom att öppna **Konfigurera**-sidan.
   > 
   > 

![Video tillgänglig](./media/storsimple-complete-minimum-device-setup/Video_icon.png) **Video tillgänglig**

Klicka [här](https://azure.microsoft.com/documentation/videos/minimum-storsimple-device-setup/) för att se en video som visar hur man slutför den minimala enhetsinstallationen.

<!--HONumber=Sep16_HO3-->


