check_gude.py
===============
- Get Sensor json data by http
  - HTTP Authentification and SSL encryption supported by optional command line parameters
- Can be used as Nagios script


Examples
===========

# 
# list all sensors (e.g. of device with IP Adresse 192.168.0.2)
#
./check_gude.py -H 192.168.0.2
L1 Meter1
        9.0.0 232.47 V Voltage
        9.0.1 0.0 A Current
        9.0.2 50.01 Hz Frequency
        9.0.3 -31.6 deg PhaseIU
        9.0.4 0.0 W ActivePower
        9.0.5 1.0 VAR ReactivePower
        9.0.6 0.0 VA ApparentPower
        9.0.7 1.0  PowerFactor
        9.0.8 0.0 A Residual Current
        9.0.9 -106.8 deg PhaseIU N
        9.0.10 -1.0 W ActivePower N
        9.0.11 -1.0 VAR ReactivePower N
        9.0.12 0.0 VA ApparentPower N
        9.0.13 -0.093  PowerFactor N
        9.0.14 0.717 kWh AbsActEnergyNonRes
        9.0.15 2.122 kVARh AbsReactEnergyNonRes
        9.0.16 0.717 kWh AbsActEnergyRes
        9.0.17 2.122 kVARh AbsReactEnergyRes
        9.0.18 7336758 s RelativeTime
        9.0.19 0.717 kWh FwdActEnergyNonRes
        9.0.20 2.122 kVARh FwdReactEnergyNonRes
        9.0.21 0.717 kWh FwdActEnergyRes
        9.0.22 2.122 kVARh FwdReactEnergyRes
        9.0.23 0.0 kWh RevActEnergyNonRes
        9.0.24 0.0 kVARh RevReactEnergyNonRes
        9.0.25 0.0 kWh RevActEnergyRes
        9.0.26 0.0 kVARh RevReactEnergyRes
1: 7105 7105
        52.0.0 27.1 deg C Temperature
        52.0.1 32.6 % Humidity
        52.0.2 9.3 deg C Dew Point
        52.0.3 17.8 deg C Dew Diff


#
# query single sensor value
#
./check_gude.py -H 192.168.0.2 --sensor 52.0.0
52.0.0 27.1 deg C Temperature

#
# query single sensor value (value only)
#
./check_gude.py -H 192.168.0.2 --sensor 53.1.0 --numeric
27.1

#
# query multiple sensor values
#
./check_gude.py -H 192.168.0.2 --sensor 53.*.0
53.2.0 Temperature 24.22 deg C
53.0.0 Temperature 24.96 deg C
53.1.0 Temperature 23.42 deg C

#
# query multiple sensor values
#
./check_gude.py -H 192.168.0.2 --sensor 53.0.*
53.0.2 Dew Point 7.74 deg C
53.0.3 Dew Diff 17.22 deg C
53.0.0 Temperature 24.96 deg C
53.0.1 Humidity 33.41 %
53.0.4 Pressure 1016.3 hPa

#
# nagios command to warn above 20 and below 10, critical above 30 and below 5
#
./check_gude.py -H 192.168.0.2 --sensor 53.0.0 --nagios -w 10:20 -c 5:30
WARNING: sensor1=24.96 (w: 5:20, c: 10:25, op:>)
esb7213-3.gudetest | sensor1=24.96;20;25

#
# nagios command to warn above 20, critical above 25
#
./check_gude.py -H 192.168.0.2 --sensor 53.0.0 --nagios -w 20 -c 25
WARNING: sensor1=24.96 (w: 20, c: 25, op:>)
esb7213-3.gudetest | sensor1=24.96;20;25

#
# nagios command to warn above 20, critical above 25
# rewrite label/unit to match nagiosgrapher config
#
./check_gude.py -H 192.168.0.2 --sensor 53.0.0 --nagios -w :20 -c :30 --label Temp --unit C
WARNING: Temp1=24.96C (w: 20, c: 30, op:>)
esb7213-3.gudetest | Temp1=24.96C;20;30

#
# nagios command to warn below 10, critical below 5
# rewrite label/unit to match nagiosgrapher config
#
./check_gude.py -H 192.168.0.2 --sensor 53.0.0 --nagios -w 10: -c 5: --label Temp --unit C
OK: Temp1=24.97C (w: 10, c: 5, op:<)
esb7213-3.gudetest | Temp1=24.97C;10;5

#
# nagios command to query multiple sensors sharing same threholds
#
./check_gude.py -H 192.168.0.2 --sensor 53.?.0 --nagios -w 27 -c 25 --label Temp --unit C
OK: Temp1=24.23C (w: 27, c: 25, op:>)
OK: Temp2=24.97C (w: 27, c: 25, op:>)
OK: Temp3=23.42C (w: 27, c: 25, op:>)
esb7213-3.gudetest | Temp1=24.23C;27;25 Temp2=24.97C;27;25 Temp3=23.42C;27;25

