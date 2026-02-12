# HDMP API Endpoints, Authentication & JSON Payload Structure

## API Endpoints Overview

### Orchestrator API Endpoints
**Base Authentication** : OAuth 2.0 Token-based (Bearer token in Authorization header)

**Primary Data Ingestion Endpoint:**
```
PUT https://server/entity/{domainCode}
```


**Other Key Endpoints:**
- `PUT https://server/gram/{domainCode}/{rootSpk}` - Partial updates (Add/Update/Delete)
- `PATCH https://server/entity/recleanse/{domainCode}/{sourcePkey}` - Re-cleanse entity
- `PATCH https://server/merge/{domainCode}` - Forced merge operations
- `POST https://server/entity/whatif/{domainCode}` - What-if cleansing

### Core Services API Endpoints  
**Base Authentication** : OAuth 2.0 Token-based + Custom Headers

**Required Headers:**
```
X-Mdx-ClientId: "ACME" (Required)
X-Mdx-CorrelationId: "5555" (Optional)  
X-Mdx-User: "integration-user@gaine.com" (Optional)
Authorization: Bearer {token}
```

**Primary Identity Management Endpoints:**
- `PUT https://server/api/SourceProfiles/{Domain}/Default` - Insert/Update source profile
- `POST https://server/api/MatchService/{Domain}/Default` - What-if matching
- `GET https://server/api/MasterProfiles/{Domain}/{Mk}` - Get master profile
- `GET https://server/api/MasterProfiles/{Domain}/{Mk}/Contributors` - Get contributors

## OAuth 2.0 Authentication Details

### Token Endpoint
```
POST https://identity.gainesolutions.com/connect/token
```

### Request Format :
```json
{
  "grant_type": "password",
  "username": "{{IdentityUsername}}",
  "password": "{{IdentityPassword}}",
  "scope": "core-services"
}
```

### Authorization Header:
```
Authorization: Basic BASE_64(AuthUsername + ":" + AuthPassword)
```

### Configuration Example :
```json
{
  "SecurityOptions": {
    "Jwt": [{
      "Name": "Orch.Jwt",
      "IssuingAuthorityServer": "https://identity.gainesolutions.com",
      "TokenPath": "/connect/token",
      "GrantType": "Password",
      "UserName": "",
      "Password": "",
      "ClientId": "",
      "ClientSecret": "",
      "Audience": "ods",
      "Scope": "orchestrator"
    }]
  }
}
```

### Token Characteristics :
- **Valid for**: Typically 8 hours
- **Usage**: Include in Authorization header as `Bearer {token}`
- **Expiration**: Returns "401 Unauthorized" when expired

## Complete JSON Payload Examples

### 1. Complete ProfileContainer Submission

**API Endpoint & Method:**
```
PUT https://server/entity/{domainCode}
Content-Type: application/json
Authorization: Bearer {your-token}
```

**Complete Provider Profile Example** :
```json
{
  "ProvProvider": {
    "SystemId": 100,
    "Spk": "SYS100.10M-AA7E15A9043427FE5520D2546B389A7C",
    "RootSpk": "SYS100.10M-AA7E15A9043427FE5520D2546B389A7C",
    "FirstName": "John",
    "LastName": "Smith",
    "NPI": "1234567890",
    "Gender": "M",
    "BirthDate": "1975-01-01T00:00:00Z",
    "ProviderStatus": "Active",
    
    "ProvAddress": [
      {
        "SystemId": 100,
        "Spk": "SYS100.10M-AA7E15A9043427FE5520D2546B389A7C.10J-10B1E049897E117CA591699D8093A9E4",
        "RootSpk": "SYS100.10M-AA7E15A9043427FE5520D2546B389A7C",
        "ParentSpk": "SYS100.10M-AA7E15A9043427FE5520D2546B389A7C",
        "AddressType": "Office",
        "Address1": "123 Medical Plaza",
        "City": "San Diego",
        "State": "CA",
        "ZipCode": "92101",
        "PrimaryAddressInd": true
      }
    ],
    
    "ProvPhone": [
      {
        "SystemId": 100,
        "Spk": "SYS100.10M-AA7E15A9043427FE5520D2546B389A7C.10K-20B1E049897E117CA591699D8093A9E5",
        "RootSpk": "SYS100.10M-AA7E15A9043427FE5520D2546B389A7C",
        "ParentSpk": "SYS100.10M-AA7E15A9043427FE5520D2546B389A7C",
        "PhoneType": "Office",
        "PhoneNumber": "619-555-1234",
        "Extension": "101"
      }
    ],

    "ProvLicense": [
      {
        "SystemId": 100,
        "Spk": "SYS100.10M-AA7E15A9043427FE5520D2546B389A7C.10L-30C1F049897E117CA591699D8093A9F6",
        "RootSpk": "SYS100.10M-AA7E15A9043427FE5520D2546B389A7C",
        "ParentSpk": "SYS100.10M-AA7E15A9043427FE5520D2546B389A7C",
        "LicenseNumber": "L123456",
        "LicenseState": "CA",
        "LicenseType": "Medical",
        "ExpirationDate": "2025-12-31T00:00:00Z"
      }
    ]
  }
}
```

### 2. Required vs Optional Fields

**Required Fields (Universal)** :
- `SystemId` (int): Source system identifier
- `Spk` (string): Source Primary Key  
- `RootSpk` (string): Root entity identifier

**Required for Child Entities** :
- `ParentSpk` (string): Parent entity's SPK

**Optional Fields**:
- All business attributes (depends on Model Attributes configuration)
- Domain-specific fields (varies by entity type)

### 3. Child Entity Nesting Structure

Child entities are **nested as arrays** within their parent entity :

```json
{
  "ParentEntity": {
    "SystemId": 100,
    "Spk": "SYS100.ROOT",
    "RootSpk": "SYS100.ROOT",
    "ParentAttribute1": "Value",
    
    "ChildEntityName": [
      {
        "SystemId": 100,
        "Spk": "SYS100.ROOT.CHILD1",
        "RootSpk": "SYS100.ROOT",
        "ParentSpk": "SYS100.ROOT",
        "ChildAttribute1": "Value"
      }
    ],
    
    "AnotherChildEntity": [
      {
        "SystemId": 100,
        "Spk": "SYS100.ROOT.CHILD2", 
        "RootSpk": "SYS100.ROOT",
        "ParentSpk": "SYS100.ROOT",
        "ChildAttribute2": "Value"
      }
    ]
  }
}
```

### 4. Partial Update (Gram) Example

**API Endpoint:**
```
PUT https://server/gram/{domainCode}/{rootSpk}
```

**Payload Structure** :
```json
{
  "Add": [
    {
      "EntityName": "ProvAddress",
      "RootSpk": "SYS001.AFE32CDFF6B",
      "Spk": "SYS001.1AFE32CDFF6B.PROV_ADDR999", 
      "ParentSpk": "SYS001.AFE32CDFF6B",
      "SystemId": 1,
      "DataAttributes": {
        "AddressType": "Commercial",
        "Address1": "456 New Street",
        "City": "Los Angeles",
        "State": "CA"
      }
    }
  ],
  "Update": [
    {
      "EntityName": "ProvAddress",
      "RootSpk": "SYS001.AFE32CDFF6B",
      "Spk": "SYS001.AFE32CDFF6B.EXISTING_ADDR",
      "DataAttributes": {
        "AddressType": "Residential",
        "PrimaryAddressInd": null
      }
    }
  ],
  "Delete": [
    {
      "EntityName": "ProvPhone",
      "Spk": "SYS001.AFE32CDFF6B.OLD_PHONE"
    }
  ]
}
```

## Success Response Format

**Standard Response** :
```json
{
  "jsonapi": { "version": "1.0" },
  "meta": {
    "requestId": "638dd64e-e28b-45c3-8773-8d9a6d7ae5fa",
    "success": "true",
    "duration": "45",
    "responseCount": "3",
    "httpStatusCode": "200",
    "webActivityLogId": "87",
    "jobActivityLogId": "353"
  },
  "data": [
    {
      "model": "Image",
      "keyName": "Spk", 
      "keyValue": "SYS001.TEST_PROVIDER1"
    },
    {
      "model": "Xref",
      "keyName": "Spk",
      "keyValue": "SYS001.TEST_PROVIDER1"
    },
    {
      "model": "Master",
      "keyName": "Mk",
      "keyValue": "1000000176"
    }
  ]
}
```

The exact JSON structure varies based on your configured data model - refer to your CLI project configuration for specific entity and attribute definitions .
