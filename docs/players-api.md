# Players API Documentation
aaaaaaaaaa
## Overview

The Players API provides endpoints for managing player accounts, authentication, and activity tracking in your game or application.

## Authentication

Most endpoints require authentication. Use the `/api/Players/login` endpoint to obtain a player ID that can be used for subsequent requests.

## Endpoints

### Get All Players

```
GET /api/Players
```

Retrieves a list of all players, with optional filtering.

**Query Parameters:**
- Various filter parameters can be passed via the `getPlayersQuery` object

**Responses:**
- 200: Success - Returns an array of PlayerViewDto objects
- 404: Not Found - No players match the criteria
- 500: Server Error

### Create Player

```
POST /api/Players
```

Creates a new player account.

**Request Body:**
- CreatePlayerCommand object

**Responses:**
- 200: Success - Returns the new player's GUID

### Update Player

```
PUT /api/Players/{id}
```

Updates an existing player's information.

**Path Parameters:**
- id: The GUID of the player to update

**Request Body:**
- CreatePlayerDTO object

**Responses:**
- 200: Success

### Get Player Details

```
GET /api/Players/uuid
```

Retrieves detailed information about a specific player.

**Query Parameters:**
- query: GetPlayerDetailsByIdQuery object

**Responses:**
- 200: Success - Returns a Player object

### Delete Player

```
DELETE /api/Players
```

Deletes a player account.

**Query Parameters:**
- command: DeletePlayerCommand object

**Responses:**
- 200: Success

### Login Player

```
POST /api/Players/login
```

Authenticates a player and creates a session.

**Request Body:**
- LoginPlayerCommand object

**Responses:**
- 200: Success - Returns the player's ID
- 401: Unauthorized - Invalid credentials

### Logout Player

```
POST /api/Players/logout
```

Ends a player's session.

**Request Body:**
- LogoutPlayerCommand object

**Responses:**
- 200: Success

## Models

### Player

The core player entity.

```csharp
public class Player : Entity<Guid>
{
    public string Name { get; set; }
    public string Account { get; set; }
    public string Password { get; set; }
    public Status Status { get; set; } = Status.Active;
    public ICollection<PlayerLog>? PlayerLogs { get; set; }
    public Guid ProjectID { get; set; }
    public Project? Project { get; set; }
}
```

### PlayerLog

Tracks player activity.

```csharp
public class PlayerLog : Entity<Guid>
{
    public DateTime CheckInTime { get; set; }
    public DateTime CheckOutTime { get; set; }
    public Guid PlayerID { get; set; }
    public Player? Player { get; set; }
}
```

## Status Enum

```csharp
public enum Status
{
    Active = 0,
    Inactive = 1,
    Suspended = 2,
    Banned = 3
}
```
