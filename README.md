# netwoktest
cimprobar la red
import requests
import socket
import subprocess
import scapy.all as scapy
from time import sleep

# Función para comprobar la conexión a Internet
def check_internet():
    try:
        # Intentamos hacer un ping a google.com para verificar la conexión a Internet
        response = requests.get('https://www.google.com', timeout=5)
        if response.status_code == 200:
            print("Conexión a Internet: OK")
            return True
    except requests.RequestException:
        print("Conexión a Internet: Fallida")
        return False

# Función para comprobar el DNS
def check_dns():
    try:
        # Intentamos resolver un nombre de dominio
        socket.gethostbyname('google.com')
        print("DNS: OK")
        return True
    except socket.gaierror:
        print("DNS: Fallido")
        return False

# Función para escanear la red local
def scan_local_network():
    # Escaneamos las direcciones IP de la red local usando ARP
    ip_base = "192.168.1"  # Cambia esto si tu red local usa otro rango
    active_devices = []
    print("Escaneando la red local...")

    for i in range(1, 255):
        ip = f"{ip_base}.{i}"
        arp_request = scapy.ARP(pdst=ip)
        broadcast = scapy.Ether(dst="ff:ff:ff:ff:ff:ff")
        arp_request_broadcast = broadcast/arp_request
        answered_list = scapy.srp(arp_request_broadcast, timeout=1, verbose=False)[0]

        for element in answered_list:
            active_devices.append(element[1].psrc)

    print(f"Dispositivos activos en la red local: {len(active_devices)}")
    for device in active_devices:
        print(f"- {device}")

    return len(active_devices)

# Comprobamos conexión a Internet y DNS
if check_internet():
    check_dns()

# Comprobamos los dispositivos activos en la red local
active_devices_count = scan_local_network()
