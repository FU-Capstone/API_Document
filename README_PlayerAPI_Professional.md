# Player API Integration Guide for Unity Developers

This document describes the `Player` API used to manage player accounts in a Unity-integrated game backend system.

## Base URL

```
/api/players
```

---

## GET `/api/players`

Retrieve a list of players based on query filters.

### Query Parameters

|     Name     |  Type  |     Description     |
|--------------|--------|---------------------|
| `projectKey` | Guid   | Input your key here |

### Response

#### 200 OK

```json
[
  {
    "id": "08dd73fb-3db1-4cb3-8256-f8f4cbd0a0de",
    "name": "hoang",
    "account": "hoang",
    "totalPlayTime": 120,
    "status": "Active"
  }
]
```

#### 500 Internal Server Error

```json
{
  "message": "An error occurred while processing your request."
}
```
## Example integrate API:
```csharp
IEnumerator GetPlayers(string projectKey)
{
    string url = $"BASE_URL?projectKey={projectKey}";
    using UnityWebRequest request = UnityWebRequest.Get(url);
    yield return request.SendWebRequest();

    if (request.result == UnityWebRequest.Result.Success)
        Debug.Log("Response: " + request.downloadHandler.text);
    else
        Debug.LogError("Error: " + request.error);
}
```

---

## GET `/api/players/uuid`

Get full player details by `id`.

### Query Parameters

| Name | Type |           Description             |
|------|------|-----------------------------------|
| `Id` | Guid | Required. The UUID of the player. |

### Response

#### 200 OK

```json
{
  "name": "hoang",
  "account": "hoang",
  "password": "mPjhfNINyFFOc/OgiSsc1mVpXq8mWaF5zzdIbv3rV6Fu+0Z50nPmbPyQ83SKM++9",
  "status": 1,
  "playerLogs": {
    "checkInTime": "2025-04-05T04:45:07.088163+00:00",
    "checkOutTime": "2025-04-05T07:45:07.088163+00:00",
  },
  "userId": "08dd73fb-3db1-4cb3-8256-f8f4cbd0a0de",
  "projectID": "08dd73a8-7646-47ca-8a9a-e6203d256084",
  "rowVersion": "CN1z+0Ala3o=",
  "createdAt": "2025-04-05T04:35:07.088163+00:00",
  "updatedAt": "2025-04-05T04:35:07.088163+00:00",
}
```

#### 404 Not Found

```json
{
  "message": "Player not found."
}
```
## Example integrate API:
```csharp
IEnumerator GetPlayerDetailsById(string id)
{
    string url = $"BASE_URL/uuid?id={id}";
    using UnityWebRequest request = UnityWebRequest.Get(url);
    yield return request.SendWebRequest();

    if (request.result == UnityWebRequest.Result.Success)
        Debug.Log("Player Info: " + request.downloadHandler.text);
    else if (request.responseCode == 404)
        Debug.LogWarning("Player not found.");
    else
        Debug.LogError("Error: " + request.error);
}

```
---

## POST `/api/players`

Create a new player.

### Request Body

```json
{
  "name": "hoang",
  "account": "hoang123",
  "password": "yourPasswordHere",
  "status": 1,
  "projectKey": "08dd73a8-7646-47ca-8a9a-e6203d256084"
}
```

### Response

#### âœ… 200 OK

```json
"08dd73fb-3db1-4cb3-8256-f8f4cbd0a0de"
```

#### 400 Bad Request

## Example integrate API:
```csharp
[System.Serializable]
public class CreatePlayerPayload
{
    public string name;
    public string account;
    public string password;
    public int status;
    public string projectKey;
}

IEnumerator CreatePlayer(CreatePlayerPayload payload)
{
    string jsonData = JsonUtility.ToJson(payload);
    byte[] bodyRaw = System.Text.Encoding.UTF8.GetBytes(jsonData);

    using UnityWebRequest request = new UnityWebRequest("BASE_URL", "POST");
    request.uploadHandler = new UploadHandlerRaw(bodyRaw);
    request.downloadHandler = new DownloadHandlerBuffer();
    request.SetRequestHeader("Content-Type", "application/json");

    yield return request.SendWebRequest();

    if (request.result == UnityWebRequest.Result.Success)
        Debug.Log("New Player ID: " + request.downloadHandler.text);
    else
        Debug.LogError("Error: " + request.error);
}

```
---

## PUT `/api/players/{id}`

Update player information.

### Path Parameter

| Name | Type |          Description           |
|------|------|--------------------------------|
| `id` | Guid | Required. Player ID to update. |

### Request Body

```json
{
  "name": "UpdatedName",
  "account": "UpdatedAccount",
  "password": "newPassword",
  "status": 1,
  "projectKey": "08dd73a8-7646-47ca-8a9a-e6203d256084"
}
```

### Response

#### 200 OK

#### 400 Bad Request

## Example integrate API:
```csharp
IEnumerator UpdatePlayer(string id, CreatePlayerPayload payload)
{
    string jsonData = JsonUtility.ToJson(payload);
    byte[] bodyRaw = System.Text.Encoding.UTF8.GetBytes(jsonData);

    string url = $"BASE_URL/{id}";
    using UnityWebRequest request = UnityWebRequest.Put(url, bodyRaw);
    request.method = "PUT";
    request.SetRequestHeader("Content-Type", "application/json");

    yield return request.SendWebRequest();

    if (request.result == UnityWebRequest.Result.Success)
        Debug.Log("Player updated successfully.");
    else
        Debug.LogError("Update error: " + request.error);
}
```
---

## DELETE `/api/players`

Delete a player by ID.

### Query Parameters

| Name | Type |              Description              |
|------|------|---------------------------------------|
| `Id` | Guid | Required. ID of the player to delete. |

### Response

#### 200 OK

#### 404 Not Found

## Example integrate API:
```csharp
IEnumerator DeletePlayer(string playerId)
{
    string url = $"BASE_URL?id={playerId}";
    using UnityWebRequest request = UnityWebRequest.Delete(url);
    yield return request.SendWebRequest();

    if (request.result == UnityWebRequest.Result.Success)
        Debug.Log("Player deleted successfully.");
    else if (request.responseCode == 404)
        Debug.LogWarning("Player not found.");
    else
        Debug.LogError("Delete error: " + request.error);
}
```
---

## POST `/api/players/login`

Login player.

### Request Body

```json
{
  "account": "hoang123",
  "password": "yourPassword"
}
```

### Response

#### 200 OK

```json
{
  "playerId": "08dd73fb-3db1-4cb3-8256-f8f4cbd0a0de"
}
```

#### 401 Unauthorized

```json
{
  "message": "Invalid account or password."
}
```

## Example integrate API:
```csharp
[System.Serializable]
public class LoginPayload
{
    public string account;
    public string password;
}

IEnumerator LoginPlayer(LoginPayload payload)
{
    string jsonData = JsonUtility.ToJson(payload);
    byte[] bodyRaw = System.Text.Encoding.UTF8.GetBytes(jsonData);

    using UnityWebRequest request = new UnityWebRequest("BASE_URL/login", "POST");
    request.uploadHandler = new UploadHandlerRaw(bodyRaw);
    request.downloadHandler = new DownloadHandlerBuffer();
    request.SetRequestHeader("Content-Type", "application/json");

    yield return request.SendWebRequest();

    if (request.result == UnityWebRequest.Result.Success)
        Debug.Log("Login successful: " + request.downloadHandler.text);
    else
        Debug.LogError("Login failed: " + request.error);
}

```
---

## POST `/api/players/logout`

Logout player.

### Request Body

```json
{
  "playerId": "08dd73fb-3db1-4cb3-8256-f8f4cbd0a0de"
}
```

### Response

#### 200 OK

## Example integrate API:
```csharp
[System.Serializable]
public class LogoutPayload
{
    public string playerId;
}

IEnumerator LogoutPlayer(LogoutPayload payload)
{
    string jsonData = JsonUtility.ToJson(payload);
    byte[] bodyRaw = System.Text.Encoding.UTF8.GetBytes(jsonData);

    using UnityWebRequest request = new UnityWebRequest("BASE_URL/logout", "POST");
    request.uploadHandler = new UploadHandlerRaw(bodyRaw);
    request.downloadHandler = new DownloadHandlerBuffer();
    request.SetRequestHeader("Content-Type", "application/json");

    yield return request.SendWebRequest();

    if (request.result == UnityWebRequest.Result.Success)
        Debug.Log("Logout successful.");
    else
        Debug.LogError("Logout failed: " + request.error);
}
```
---

©2025 GameHub API | All rights reserved.