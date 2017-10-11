## <a name="customize-and-extend-the-device-management-actions"></a>Anpassa och utöka enheten hanteringsåtgärder

IoT-lösningar kan utöka definierad uppsättning device management mönster eller aktivera anpassade mönster genom att använda enheten dubbla och moln till enhet metoden primitiver. Andra exempel på enheten hanteringsåtgärder är fabriksåterställning, firmware-uppdatering, programuppdatering, energisparfunktioner, nätverk och anslutning hantering och kryptering av data.

## <a name="device-maintenance-windows"></a>Underhållsperioder för enhet

Normalt kan du konfigurera enheter för att utföra åtgärder på den tid som minimerar avbrott och driftstopp. Enheten underhållsfönster är ett mönster som används ofta för att definiera den tid när en enhet ska uppdatera konfigurationen. Dina backend-lösningar kan använda önskade egenskaper för enhet dubbla att definiera och aktivera en princip på enheten som möjliggör en underhållsperiod. När en enhet tar emot princip för underhåll av fönstret, kan enheten dubbla rapporterade egenskapen användas för att rapportera status för principen. Backend-app kan sedan använda enheten dubbla frågor för att bekräfta att kompatibiliteten för enheter och varje princip.

## <a name="next-steps"></a>Nästa steg

I den här kursen används direkt metod för att utlösa en fjärransluten omstart på en enhet. Du används egenskaperna rapporterade för att rapportera senast omstart från enheten och frågas enheten identiska för att identifiera senast omstart av enheten från molnet.

Om du vill fortsätta komma igång med IoT-hubb och device management mönster, till exempel remote via luften firmware-uppdatering, se:

[Självstudier: Hur du gör en firmware-uppdatering][lnk-fwupdate]

Information om hur du utökar din IoT-lösningen och schema metodanrop på flera enheter finns i [schema och broadcast jobb] [ lnk-tutorial-jobs] kursen.

Om du vill fortsätta komma igång med IoT-hubb finns [komma igång med IoT kant][lnk-iot-edge].

[lnk-fwupdate]: ../articles/iot-hub/iot-hub-node-node-firmware-update.md
[lnk-tutorial-jobs]: ../articles/iot-hub/iot-hub-node-node-schedule-jobs.md
[lnk-iot-edge]: ../articles/iot-hub/iot-hub-linux-iot-edge-get-started.md