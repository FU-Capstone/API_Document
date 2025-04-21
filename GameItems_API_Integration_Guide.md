# Game Items API Integration Guide for Unity Developers

Welcome! This guide helps Unity developers easily integrate Game Items API into their game projects. Whether you're fetching, creating, updating, or deleting game items, this documentation has you covered.

> ‚úÖ Base URL: `http://localhost:8080/api/GameItems`

---

## Endpoints Overview

| Method | Endpoint             | Description                      |
|--------|----------------------|----------------------------------|
| GET    | `/api/GameItems`     | Get all game items by projectKey |
| GET    | `/api/GameItems/{id}`| Get details of a specific item   |
| POST   | `/api/GameItems`     | Create a new game item           |
| PUT    | `/api/GameItems/{id}`| Update a game item               |
| DELETE | `/api/GameItems/{id}`| Delete a game item               |

---

## 1. Get All Game Items

### `GET /api/GameItems?projectKey=<GUID>`

###  Parameters

| Name        | Type  | Required | Description               |
|-------------|-------|----------|---------------------------|
| projectKey  | GUID  | ‚úÖ        | Unique identifier of game |

### Sample Response

```json
[
  {
    "id": "d1a1-1234...",
    "name": "Speed Potion",
    "type": "Consumable",
    "rarity": "Rare",
    "description": "Boosts speed for 20s",
    "image": { 
      "url": "https://cdn.example.com/items/health_potion.png",
      "format": "png",
      "altText": "Health Potion" 
    },
    "attributes": {
      "speedBoost": 0.3,
      "duration": 20
    },
    "createdAt": "2025-04-09T10:12:00Z"
  }
]
```

---

## 2. Get Game Item By ID

### `GET /api/GameItems/{id}`

### Sample Response

```json
{
  "id": "d1a1-1234...",
  "name": "Speed Potion",
  "type": "Consumable",
  "rarity": "Rare",
  "description": "Boosts speed for 20s",
  "image": { 
    "url": "https://cdn.example.com/items/health_potion.png",
    "format": "png",
    "altText": "Health Potion" 
  },
  "attributes": {
    "speedBoost": 0.3,
    "duration": 20
  },
  "createdAt": "2025-04-09T10:12:00Z"
}
```

---

##† 3. Create New Game Item

### `POST /api/GameItems`

### Request Body

```json
{
  "createGameItemDto": {
    "name": "Fire Sword",
    "type": "Weapon",
    "rarity": "Epic",
    "description": "Inflicts fire damage",
    "image": {
      "url": "https://cdn.example.com/items/health_potion.png",
      "format": "png",
      "altText": "Health Potion" 
    },
    "attributes": {
      "damage": 40,
      "fireEffect": true
    },
    "projectKey": "GUID-HERE"
  }
}
```

### Response

```json
"GUID-OF-CREATED-ITEM"
```

---

##† 4. Update Game Item

### `PUT /api/GameItems/{id}`

### Request Body

```json
{
  "name": "Fire Sword v2",
  "type": "Weapon",
  "rarity": "Legendary",
  "description": "Now with more flame!",
  "image": {
    "url": "https://cdn.example.com/items/health_potion.png",
    "format": "png",
    "altText": "Health Potion" 
  },
  "attributes": {
    "damage": 55,
    "burnDuration": 5
  },
  "projectKey": "GUID-HERE"
}
```

### Response

- `204 No Content` (successful)
- `404 Not Found` if item doesn't exist

---

##  5. Delete Game Item

### `DELETE /api/GameItems/{id}`

### Response

- `204 No Content` if success
- `404 Not Found` if item doesn't exist

---

## GameItemViewDto Structure

| Field        | Type                       | Description                       |
|--------------|----------------------------|-----------------------------------|
| `id`         | Guid                       | Unique identifier                 |
| `name`       | string                     | Name of the item                  |
| `type`       | string                     | Item type (e.g., Weapon, Spell)   |
| `rarity`     | string                     | Rarity level (e.g., Common, Rare) |
| `description`| string                     | Description                       |
| `image.url`  | string (URL)               | Image URL                         |
| `attributes` | Dictionary<string, object> | Custom item properties            |
| `createdAt`  | ISO 8601 DateTime          | Created date                      |

---

## Tips for Unity Developers

- Use `UnityWebRequest` or `RestClient` to interact with the API.
- Deserialize the `attributes` dictionary using `Newtonsoft.Json` or a custom converter.
- Use `image.url` to load sprites/textures dynamically.
- Store your `projectKey` securely if shared across APIs.

---

## Example Unity C# (Get Items)

```csharp
using UnityEngine.Networking;

public IEnumerator GetGameItems()
{
    string url = "http://localhost:8080/api/GameItems?projectKey=YOUR_PROJECT_KEY";
    UnityWebRequest request = UnityWebRequest.Get(url);
    yield return request.SendWebRequest();

    if (request.result == UnityWebRequest.Result.Success)
    {
        Debug.Log("Items: " + request.downloadHandler.text);
    }
    else
    {
        Debug.LogError("Error: " + request.error);
    }
}
```

---

## Need Support?

> Found a bug or need integration help? Contact your API team or open a support ticket.

---

Made with ‚ù§Ô∏è for Unity Developers.


---

## Unity C# Sample ‚Äì Create Game Item (POST)

```csharp
using UnityEngine;
using UnityEngine.Networking;
using System.Text;
using System.Collections;

public IEnumerator CreateGameItem()
{
    string url = "http://localhost:8080/api/GameItems";
    string jsonData = @"{
        ""createGameItemDto"": {
            ""name"": ""Ice Shield"",
            ""type"": ""Defense"",
            ""rarity"": ""Epic"",
            ""description"": ""Blocks ice damage"",
            ""image"": {
              ""url"": ""https://cdn.example.com/items/health_potion.png"", ""format"": ""png"", ""altText"": ""Health Potion""
            },
            ""attributes"": {
                ""block"": 30,
                ""freezeResistance"": true
            },
            ""projectKey"": ""YOUR_PROJECT_KEY""
        }
    }";

    UnityWebRequest request = new UnityWebRequest(url, "POST");
    byte[] bodyRaw = Encoding.UTF8.GetBytes(jsonData);
    request.uploadHandler = new UploadHandlerRaw(bodyRaw);
    request.downloadHandler = new DownloadHandlerBuffer();
    request.SetRequestHeader("Content-Type", "application/json");

    yield return request.SendWebRequest();

    if (request.result == UnityWebRequest.Result.Success)
    {
        Debug.Log("Created Item ID: " + request.downloadHandler.text);
    }
    else
    {
        Debug.LogError("Error: " + request.error);
    }
}
```

---

##† Unity C# Sample ‚Äì Update Game Item (PUT)

```csharp
public IEnumerator UpdateGameItem(string itemId)
{
    string url = $"http://localhost:8080/api/GameItems/{itemId}";
    string jsonData = @"{
        ""name"": ""Ice Shield+1"",
        ""type"": ""Defense"",
        ""rarity"": ""Legendary"",
        ""description"": ""Enhanced ice shield"",
        ""image"": { ""url"": ""https://cdn.example.com/items/health_potion.png"", ""format"": ""png"", ""altText"": ""Health Potion"" },
        ""attributes"": {
            ""block"": 45,
            ""freezeResistance"": true
        },
        ""projectKey"": ""YOUR_PROJECT_KEY""
    }";

    UnityWebRequest request = new UnityWebRequest(url, "PUT");
    byte[] bodyRaw = Encoding.UTF8.GetBytes(jsonData);
    request.uploadHandler = new UploadHandlerRaw(bodyRaw);
    request.downloadHandler = new DownloadHandlerBuffer();
    request.SetRequestHeader("Content-Type", "application/json");

    yield return request.SendWebRequest();

    if (request.result == UnityWebRequest.Result.Success || request.responseCode == 204)
    {
        Debug.Log("Item updated successfully");
    }
    else
    {
        Debug.LogError("Update error: " + request.error);
    }
}
```

---

## Unity C# Sample ‚Äì Delete Game Item (DELETE)

```csharp
public IEnumerator DeleteGameItem(string itemId)
{
    string url = $"http://localhost:8080/api/GameItems/{itemId}";
    UnityWebRequest request = UnityWebRequest.Delete(url);

    yield return request.SendWebRequest();

    if (request.result == UnityWebRequest.Result.Success || request.responseCode == 204)
    {
        Debug.Log("Item deleted successfully");
    }
    else
    {
        Debug.LogError("Delete error: " + request.error);
    }
}
```

©2025 GameHub API | All rights reserved.