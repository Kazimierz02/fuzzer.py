import requests
import sys

def main():
    print("--- Mini Web Directory Fuzzer ---")
    print("Ostrzeżenie: Używaj tylko na autoryzowanych celach!\n")

    if len(sys.argv) != 3:
        print("Użycie: python fuzzer.py <URL> <plik_ze_slowami>")
        print("Przykład: python fuzzer.py http://testphp.vulnweb.com wordlist.txt")
        sys.exit(1)

    url = sys.argv[1]
    wordlist_path = sys.argv[2]

    if not url.endswith('/'):
        url += '/'

    try:
        with open(wordlist_path, 'r') as file:
            directories = file.read().splitlines()
    except FileNotFoundError:
        print(f"Błąd: Nie znaleziono pliku {wordlist_path}")
        sys.exit(1)

    print(f"Rozpoczynam skanowanie celu: {url}")
    print(f"Wczytano słów: {len(directories)}\n")

    for directory in directories:
        target_url = f"{url}{directory}"
        try:
            response = requests.get(target_url, timeout=3)
            
            if response.status_code == 200:
                print(f"[+] ZNALEZIONO (200): {target_url}")
            elif response.status_code == 403:
                print(f"[-] DOSTĘP ZABRONIONY (403): {target_url}")
                
        except requests.exceptions.RequestException as e:
            print(f"Błąd połączenia z {target_url}")
            break

if __name__ == "__main__":
    main()
