<!--author=alkohli last changed: 9/16/15-->

#### So verkabeln Sie das Gerät für die Stromversorgung

1. Stellen Sie sicher, dass sich die Netzschalter aller Stromversorgungs- und Kühleinheiten (Power and Cooling Modules, PCMs) in Position OFF befinden.

2. Schließen Sie an jedes PCM im primären Gehäuse ein Netzkabel an.

3. Schließen Sie die Netzkabel an die Rack-PDUs an, wie in der folgenden Abbildung dargestellt. Stellen Sie sicher, dass die beiden PCMs separate Stromquellen verwenden.

    >[AZURE.IMPORTANT] Um hohe Verfügbarkeit für Ihr System sichergestellt ist, sollten Sie unbedingt das in der folgenden Abbildung gezeigte stromverkabelungsschema einhalten. 

    ![Stromverkabelung des 2 HE-Geräts](./media/storsimple-cable-8100-for-power/HCSCableYour2UDeviceforPower.png)

    **Stromverkabelung eines 8100-Geräts**

    |Bezeichnung|Beschreibung|
    |:----|:----------|
    |1|PCM 0|
    |2|Controller 1|
    |3|Controller 0|
    |4|PCM 1|
    |5|PDUs|

4. Schalten Sie das System ein, indem Sie die Netzschalter beider PCMs in die Position ON bringen.


