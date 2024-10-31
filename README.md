import requests

# Замените 'YOUR_OAUTH_TOKEN' вашим токеном
OAUTH_TOKEN = 'YOUR_OAUTH_TOKEN'
BASE_URL = 'https://cloud-api.yandex.net/v1/disk'


def upload_file_to_yandex_disk(file_path, disk_path):
#Получаем URL для загрузки файла
    upload_url = f"{BASE_URL}/resources/upload?path={disk_path}&overwrite=true"
    headers = {
        'Authorization': f'OAuth {OAUTH_TOKEN}'
    }

    response = requests.get(upload_url, headers=headers)
    
    if response.status_code == 200:
# Загружаем файл
        href = response.json().get('href')
        with open(file_path, 'rb') as file:
            upload_response = requests.put(href, files={'file': file})
            if upload_response.status_code == 201:
                print(f"Файл '{file_path}' успешно загружен на Яндекс.Диск в '{disk_path}'.")
            else:
                print(f"Ошибка при загрузке файла: {upload_response.status_code}, {upload_response.text}")
    else:
        print(f"Ошибка при получении URL для загрузки: {response.status_code}, {response.text}")


def list_files_in_root():
# Получаем список файлов в корневой директории
    list_url = f"{BASE_URL}/resources"
    headers = {
        'Authorization': f'OAuth {OAUTH_TOKEN}'
    }

    response = requests.get(list_url, headers=headers)
    
    if response.status_code == 200:
        items = response.json().get('_embedded', {}).get('items', [])
        print("Список файлов в корневой директории:")
        for item in items:
            print(f"- {item['name']}")
    else:
        print(f"Ошибка при получении списка файлов: {response.status_code}, {response.text}")


if __name__ == "__main__":
# Пример использования функций
# Загрузка файла
    upload_file_to_yandex_disk('local_file.txt', 'disk_file.txt')  # Замените на ваши имена файлов

# Получение списка файлов
    list_files_in_root()
