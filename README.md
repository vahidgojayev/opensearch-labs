# opensearch-labs

### OpenSearch create index  and document using Python 

``` import requests
import json

# OpenSearch URL'si
OPENSEARCH_URL = "https://open-cluster-cordinator-node-1:9200"

# Kimlik doğrulama bilgileri
AUTH = ("admin", "Kibana2020")

# Başlıklar
HEADERS = {"Content-Type": "application/json"}

# Mapping tanımı
mappings = {
    "mappings": {
        "properties": {
            "title": { "type": "text" },
            "description": { "type": "text" },
            "timestamp": { "type": "date" }
        }
    }
}

def create_index(index_name, mappings):
    url = f"{OPENSEARCH_URL}/{index_name}"
    try:
        response = requests.put(
            url,
            headers=HEADERS,
            auth=AUTH,
            data=json.dumps(mappings),
            verify=False
        )
        print(f"Response status code: {response.status_code}")
        print(f"Response text: {response.text}")
        response.raise_for_status()
        return response.json()
    except requests.exceptions.RequestException as e:
        print(f"HTTP Request failed: {e}")
        return None
    except json.JSONDecodeError:
        print("Invalid JSON response from OpenSearch")
        print("Response content:", response.text)
        return None


# Örnek indeks oluşturma
index_name = "mylab"
create_index(index_name, mappings)




# Belge ekleme fonksiyonu
def add_document(index_name, doc_id, document):
    url = f"{OPENSEARCH_URL}/{index_name}/_doc/{doc_id}"
    try:
        response = requests.put(  # Belirli bir doc_id ile belge eklemek için PUT kullanıyoruz
            url,
            headers=HEADERS,
            auth=AUTH,
            data=json.dumps(document),
            verify=False
        )
        print(f"Response status code: {response.status_code}")
        print(f"Response text: {response.text}")
        response.raise_for_status()
        return response.json()
    except requests.exceptions.RequestException as e:
        print(f"HTTP Request failed: {e}")
        return None
    except json.JSONDecodeError:
        print("Invalid JSON response from OpenSearch")
        print("Response content:", response.text)
        return None


# Örnek indeks adı ve belge
index_name = "mylab"
doc_id = "7"  # Belge ID'si (opsiyonel)
document = {
    "title": "ADA Demo Basics 5",
    "content": "Hello My New Way is Opened",
    "timestamp": "2024-02-20T12:00:00"
}

# Belgeyi OpenSearch'e ekleme
add_document(index_name, doc_id, document)

```

### OpenSearch query index  and document using Python 

``` import requests
import json

# OpenSearch URL'si
OPENSEARCH_URL = "https://open-cluster-cordinator-node-1:9200"

# Kimlik doğrulama bilgileri
AUTH = ("admin", "Kibana2020")

# Başlıklar
HEADERS = {"Content-Type": "application/json"}

# Mapping tanımı
mappings = {
    "mappings": {
        "properties": {
            "title": { "type": "text" },
            "description": { "type": "text" },
            "timestamp": { "type": "date" }
        }
    }
}

index_name = "mylab"

def search(index_name, query):
    url = f"{OPENSEARCH_URL}/{index_name}/_search"
    try:
        response = requests.get(
            url,
            headers=HEADERS,
            auth=AUTH,
            json=query,  # 'data' yerine 'json' daha uygun
            verify=False
        )
        print(f"Response status code: {response.status_code}")
        print(f"Response text: {response.text}")
        response.raise_for_status()
        return response.json()
    except requests.exceptions.RequestException as e:
        print(f"HTTP Request failed: {e}")
        return None
    except json.JSONDecodeError:
        print("Invalid JSON response from OpenSearch")
        print("Response content:", response.text)
        return None


query = {
    "query": {
        "wildcard": {
            "content.keyword": "*Open*"
        }
    }
}


print(search(index_name, query))
```