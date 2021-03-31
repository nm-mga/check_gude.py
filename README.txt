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
1: i006 i006
	53.0.0 24.96 deg C Temperature
	53.0.1 33.41 % Humidity
	53.0.2 7.74 deg C Dew Point
	53.0.3 17.22 deg C Dew Diff
	53.0.4 1016.3 hPa Pressure
2: 7106 7106
	53.1.0 23.42 deg C Temperature
	53.1.1 37.88 % Humidity
	53.1.2 8.23 deg C Dew Point
	53.1.3 15.19 deg C Dew Diff
	53.1.4 1015.0 hPa Pressure
3: 7106 7106
	53.2.0 24.22 deg C Temperature
	53.2.1 36.46 % Humidity
	53.2.2 8.38 deg C Dew Point
	53.2.3 15.84 deg C Dew Diff
	53.2.4 1016.18 hPa Pressure

#
# query single sensor value
#
./check_gude.py -H esb7213-3.gudetest --sensor 53.1.0
53.1.0 Temperature 23.42 deg C

#
# query single sensor value (value only)
#
./check_gude.py -H 192.168.0.2 --sensor 53.1.0 --numeric
23.42

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
./check_gude.py -H esb7213-3.gudetest --sensor 53.0.*
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
./check_gude.py -H esb7213-3.gudetest --sensor 53.0.0 --nagios -w 20 -c 25
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

