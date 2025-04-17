#  Player Game Items API Integration Guide for Unity Developers

This guide helps Unity game developers integrate the **Player Game Items API** into their games. These APIs allow your game to read and manage in-game items owned by players.

---

##  Base URL

```
https://your-api-domain.com/api/PlayerGameItems
```

---

## Endpoints

###  Get All Player Items

**Request**

```http
GET /api/PlayerGameItems?playerId=123e4567-e89b-12d3-a456-426614174000
```

**Query Parameters**
- `playerId` (GUID) - required

**Response**

```json
[
  {
    "playerGameItemId": "987e4567-e89b-12d3-a456-426614174000",
    "gameItemId": "aaa12345-e89b-12d3-a456-426614174000",
    "name": "Flame Sword",
    "type": "Weapon",
    "rarity": "Epic",
    "image": {
      "url": "https://cdn.yourgame.com/images/items/flame_sword.png",
      "format": "png",
      "altText": "A blazing sword with red flames"
    },
    "attributes": {
      "attack": 45,
      "speed": 7
    },
    "quantity": 2
  }
]
```
## Sample Unity Usage (C#)

```csharp
using UnityEngine;
using UnityEngine.Networking;
using System.Collections;

IEnumerator GetPlayerItems(string playerId)
{
    string url = $"https://your-api-domain.com/api/PlayerGameItems?playerId={playerId}";
    UnityWebRequest request = UnityWebRequest.Get(url);
    yield return request.SendWebRequest();

    if (request.result == UnityWebRequest.Result.Success)
    {
        Debug.Log("Response: " + request.downloadHandler.text);
        // You can deserialize the JSON and use it in-game
    }
    else
    {
        Debug.LogError("Error: " + request.error);
    }
}
```

---

### Add Item to Player

**Request**

```http
POST /api/PlayerGameItems
Content-Type: application/json
```

**Body**

```json
{
  "playerId": "123e4567-e89b-12d3-a456-426614174000",
  "gameItemId": "aaa12345-e89b-12d3-a456-426614174000",
}
```

**Response**

```json
"987e4567-e89b-12d3-a456-426614174000"
```

Returns the unique `playerGameItemId`.

##Sample Unity Usage (C#)
```csharp
using UnityEngine;
using UnityEngine.Networking;
using System.Collections;
using System.Text;

IEnumerator AddItemToPlayer(string playerId, string gameItemId)
{
    string url = "https://your-api-domain.com/api/PlayerGameItems";

    var bodyJson = JsonUtility.ToJson(new
    {
        playerId = playerId,
        gameItemId = gameItemId
    });

    UnityWebRequest request = new UnityWebRequest(url, "POST");
    byte[] bodyRaw = Encoding.UTF8.GetBytes(bodyJson);
    request.uploadHandler = new UploadHandlerRaw(bodyRaw);
    request.downloadHandler = new DownloadHandlerBuffer();
    request.SetRequestHeader("Content-Type", "application/json");

    yield return request.SendWebRequest();

    if (request.result == UnityWebRequest.Result.Success)
    {
        Debug.Log("Item Added: " + request.downloadHandler.text); // ID of added item
    }
    else
    {
        Debug.LogError("Error: " + request.error);
    }
}

```
---

### Update Player Game Item

**Request**

```http
PUT /api/PlayerGameItems
Content-Type: application/json
```

**Body**

```json
{
  "playerGameItemId": "987e4567-e89b-12d3-a456-426614174000",
  "quantity": 5
}
```

**Response**
```http
204 No Content
```
## Sample Unity Usage (C#)
```csharp
using UnityEngine;
using UnityEngine.Networking;
using System.Collections;
using System.Text;

IEnumerator UpdatePlayerItemQuantity(string playerGameItemId, int quantity)
{
    string url = "https://your-api-domain.com/api/PlayerGameItems";

    var bodyJson = JsonUtility.ToJson(new
    {
        playerGameItemId = playerGameItemId,
        quantity = quantity
    });

    UnityWebRequest request = new UnityWebRequest(url, "PUT");
    byte[] bodyRaw = Encoding.UTF8.GetBytes(bodyJson);
    request.uploadHandler = new UploadHandlerRaw(bodyRaw);
    request.downloadHandler = new DownloadHandlerBuffer();
    request.SetRequestHeader("Content-Type", "application/json");

    yield return request.SendWebRequest();

    if (request.result == UnityWebRequest.Result.Success || request.responseCode == 204)
    {
        Debug.Log("Item quantity updated successfully.");
    }
    else
    {
        Debug.LogError("Update failed: " + request.error);
    }
}

```
---

### Delete Player Game Item

**Request**

```http
DELETE /api/PlayerGameItems?playerGameItemId=987e4567-e89b-12d3-a456-426614174000
```

**Query Parameters**
- `playerGameItemId` (GUID) - required

**Response**
```http
204 No Content
```
## Sample Unity Usage (C#)
```csharp
using UnityEngine;
using UnityEngine.Networking;
using System.Collections;

IEnumerator DeletePlayerItem(string playerGameItemId)
{
    string url = $"https://your-api-domain.com/api/PlayerGameItems?playerGameItemId={playerGameItemId}";

    UnityWebRequest request = UnityWebRequest.Delete(url);
    yield return request.SendWebRequest();

    if (request.result == UnityWebRequest.Result.Success || request.responseCode == 204)
    {
        Debug.Log("Item deleted successfully.");
    }
    else
    {
        Debug.LogError("Delete failed: " + request.error);
    }
}

```
---

## Notes for Unity Developers

- All requests and responses use **JSON** format.
- Ensure you handle response status codes properly:
  - `200 OK` for successful `GET` and `POST`
  - `204 No Content` for successful `PUT` and `DELETE`
- You can call these APIs using Unity’s `UnityWebRequest` or a networking library like `RestSharp` or `HttpClient`.


© 2025 GameHub API | All rights reserved.