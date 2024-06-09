# scannerx
import nmap

def banner_grab(target_host, target_ports):
  """
  Performs banner grabbing on a target host for specified ports using Nmap.

  Args:
      target_host (str): The hostname or IP address of the target system.
      target_ports (str): Comma-separated string of ports to scan (e.g., "80,443").

  Returns:
      dict: A dictionary containing scan results, with keys as ports and values as banners.
  """

  scanner = nmap.PortScanner()

  scan_results = {}
  try:
    scanner.scan(target_host, arguments=f"-sT -p {target_ports}")  # TCP scan
    for host, hostinfo in scanner.scan().items():
      for port, portinfo in hostinfo['ports'].items():
        if portinfo['state'] == {'state': 'open', 'reason': 'syn-ack'}:
          # Extract banner only if port is open and TCP connection established (syn-ack)
          try:
            # Attempt to connect on open port (may not always provide banner)
            sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
            sock.settimeout(5)  # Set timeout to avoid hanging
            sock.connect((target_host, port))
            banner = sock.recv(1024).decode('utf-8', errors='replace')  # Decode with error handling
            scan_results[port] = banner.strip()  # Remove leading/trailing whitespace
          except Exception as e:
            scan_results[port] = f"Error: {e}"
          finally:
            sock.close()  # Ensure socket closure
  except nmap.PortScannerError as e:
    scan_results = {"error": f"Nmap Error: {e}"}

  return scan_results

# Example usage
target_host = "www.example.com"
target_ports = "80,443"

scan_results = banner_grab(target_host, target_ports)

if "error" in scan_results:
  print(f"Error: {scan_results['error']}")
else:
  for port, banner in scan_results.items():
    print(f"Port {port}: {banner}")

this tool is under Development
