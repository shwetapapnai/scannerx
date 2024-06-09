# scannerx
import socket

def get_website_info(url):
  """
  This function retrieves basic technical information about a website.

  Args:
      url (str): The URL of the website to scan.

  Returns:
      dict: A dictionary containing technical information about the website.
  """
  # Extract hostname from URL
  hostname = url.split("//")[-1].split("/")[0]

  # Use socket library to get website information
  try:
    # Create socket object
    sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    sock.connect((hostname, 80))  # Connect to port 80 (default for HTTP)
    
    # Send HTTP request to get basic server information
    request = "HEAD / HTTP/1.1\r\nHost: {}\r\n\r\n".format(hostname)
    sock.sendall(request.encode())

    # Receive response from server
    response = sock.recv(1024).decode()
    
    # Extract server information from response header
    server_info = response.split("\r\n")[0].split(" ")[1]
    
    # Close socket connection
    sock.close()
  except Exception as e:
    server_info = "Error: Could not connect" + str(e)

  # Return dictionary with website information
  return {
    "url": url,
    "hostname": hostname,
    "server": server_info
  }

# Example usage
website_url = "https://www.example.com"
website_info = get_website_info(website_url)

print("Website Information:")
print("URL:", website_info["url"])
print("Hostname:", website_info["hostname"])
print("Server:", website_info["server"])

this tool is under Development
