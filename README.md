# IoT Temperature Monitoring and Email Alert System

A smart temperature monitoring system built in Cisco Packet Tracer. A temperature sensor is read continuously by a Single Board Computer (SBC) running a Python program. When the temperature becomes too high, the system turns on a siren and automatically sends an alert email to the administrator.

---

##  Overview

Overheating can damage equipment or cause fires, and nobody can watch a sensor all day. This project solves that by automating the response: the system checks the temperature every 2 seconds, sounds an alarm locally, and notifies the administrator by email, so the problem is noticed even from a distance.

##  Key Features

- **Continuous monitoring:** the temperature is read every 2 seconds.
- **Local alarm:** a siren turns on as soon as the temperature exceeds the limit and turns off when it returns to normal.
- **Email notification:** an alert email is sent automatically to the administrator.
- **Live display:** a temperature monitor shows the current reading.
- **Networked system:** devices communicate through a wired and wireless network with a central IoT server.

##  Components

| Component | Role |
|---|---|
| SBC-PT (SBC1) | Controller that runs the Python program |
| Temperature Sensor (IoT4) | Measures the temperature |
| Siren (IoT5) | Sounds the alarm when the temperature is high |
| Temperature Monitor | Displays the current temperature |
| IoT Server (Server-PT) | Hosts the email service and manages IoT devices |
| Switch 2950-24 | Connects the wired devices |
| Wireless Router (WRT300N) | Provides wireless connectivity |
| Laptop | Receives the alert email |

##  Network

| Device | IP Address |
|---|---|
| Switch / Gateway | [200.1.1.1] |
| IoT Server | [200.1.1.2] |
| Laptop | [200.1.1.4] |
| SBC1 | [200.1.1.5] |


##  How It Works

```
Temperature sensor → SBC reads value → Value ≥ 600 ?
                                           │
                     ┌─────────────────────┴─────────────────────┐
                    Yes                                          No
         Send alert email + Siren ON                    Siren OFF, keep monitoring
```

1. The SBC reads the temperature sensor every 2 seconds.
2. If the reading is **600 or higher**, the SBC sends an email with the subject *"Temprature Alert"* to `pc@cisco.com` and switches the siren **ON**.
3. If the reading is below 600, the siren stays **OFF** and the status is printed as normal.
4. The administrator receives the email on the laptop.

##  Code

The program is written in **Python** and runs on the SBC. File: [`Code.py`]

| Part | Purpose |
|---|---|
| `EmailClient.setup(...)` | Connects the SBC to the email server with its account |
| `digitalRead(0)` | Reads the temperature sensor |
| `EmailClient.send(...)` | Sends the alert email |
| `customWrite(0, HIGH / LOW)` | Turns the siren on or off |
| `sleep(2)` | Repeats the check every 2 seconds |

Main logic:

```python

from email import *
from time import *
from gpio import *

def onEmailReceive(sender, subject, body):
	print("Received from: " + sender)
	print("Subject: " + subject)
	print("Body: " + body)

def onEmailSend(status):
	print("send status: " + str(status))

def main():
	EmailClient.setup(
		"mcu@cisco.com",
		"cisco.com",
		"mcu",
		"password"
	)
	
	EmailClient.onReceive(onEmailReceive)
	EmailClient.onSend(onEmailSend)
	
	while True:
		temp = digitalRead(0);
		print('Temprature ', temp)
		if (temp>=600):
			EmailClient.send("pc@cisco.com", "Temprature Alert", "temprater is high")
			print('tempratur is high')
			customWrite(0,HIGH)
		else:
			print('temprature is normal')
			customWrite(0,LOW)
			
		sleep(2)	
	

	# check email once a while
	while True:
		EmailClient.receive()
		sleep(5)

if __name__ == "__main__":
	main()

```

##  How to Run

1. Install **Cisco Packet Tracer** [ 8.2.1 64Bit ].
2. Download `IoT Temperature Monitoring Email Alert.pkt` from this repository and open it.
3. Make sure the **Email service** is enabled on the IoT Server with the accounts used by the SBC and the laptop [account details].
4. Open **SBC1**, then **Programming**, and run `Code.py`.
5. Click the temperature sensor and raise the temperature above the limit.
6. Watch the siren turn on, then open the email client on the laptop and check the alert.

##  Expected Results

| Condition | Output on SBC | Siren | Email |
|---|---|---|---|
| Normal temperature | `temprature is normal` | Off | None |
| High temperature | `tempratur is high` | On | Alert sent to the administrator |

##  Repository Structure

```
├── IoT Temperature Monitoring Email Alert.pkt   # Packet Tracer project file
├── Code/
│   └── Code.py                  # Python program for the SBC
├── Images/
│   └── Topology.png             # Network and devices
└── README.md
```

## 🔮 Future Improvements

- Add a lower temperature limit for cold alerts.
- Send repeated alerts only when the state changes, to avoid duplicate emails.
- Add a fan or cooling device that turns on automatically.

##  Skills Demonstrated

- IoT system design in Cisco Packet Tracer
- Python programming for embedded devices (SBC)
- Sensor reading and actuator control
- Email service integration for automated alerts
- Basic network configuration (switch, wireless router, server)
