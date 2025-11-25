# Brazebee SDK

Official TypeScript SDK for tracking customer journey events with Brazebee.

## Installation

```bash
npm install brazebee-sdk
```

## Quick Start

```typescript
import { BrazebeeClient } from 'brazebee-sdk';

// Initialize the client
const client = new BrazebeeClient({
  apiKeyAuth: 'brzb_live_your_api_key_here',
  environment: 'https://api.brazebee.com'
});

// Track an event
await client.events.track({
  company: {
    id: 'acme_corp',
    name: 'Acme Corporation',
    paymentProvider: 'stripe',
    paymentProviderId: 'cus_abc123',
    crmProvider: 'salesforce',
    crmProviderId: '001500000ABCDEFGH'
  },
  user: {
    id: 'user_123',
    email: 'john@acme.com',
    name: 'John Doe'
  },
  eventKey: 'feature_used',
  data: {
    feature: 'export',
    format: 'csv'
  }
});
```

## Usage

### Track User Events

Track events with user context:

```typescript
await client.events.track({
  company: {
    id: 'acme_corp',
    name: 'Acme Corporation'
  },
  user: {
    id: 'user_123',
    email: 'john@acme.com',
    name: 'John Doe'
  },
  eventKey: 'onboarding_completed',
  data: {
    steps_completed: 5,
    time_taken_seconds: 180
  }
});
```

### Track System Events

Track company-level events without a specific user:

```typescript
await client.events.track({
  company: {
    id: 'acme_corp',
    name: 'Acme Corporation'
  },
  eventKey: 'plan_upgraded',
  data: {
    from_plan: 'starter',
    to_plan: 'professional',
    mrr_change: 50
  }
});
```

### Custom Fields

Both `company` and `user` objects support custom fields:

```typescript
await client.events.track({
  company: {
    id: 'acme_corp',
    name: 'Acme Corporation',
    // Standard optional fields
    paymentProvider: 'stripe',
    paymentProviderId: 'cus_abc123',
    crmProvider: 'salesforce',
    crmProviderId: '001abc',
    // Custom fields in data
  },
  user: {
    id: 'user_123',
    email: 'john@acme.com',
    name: 'John Doe'
  },
  eventKey: 'feature_used',
  data: {
    // Custom event data
    industry: 'technology',
    size: 'enterprise',
    plan: 'professional',
    mrr: 500,
    role: 'admin'
  }
});
```

## Configuration

```typescript
const client = new BrazebeeClient({
  apiKeyAuth: 'brzb_live_your_api_key_here',  // Required: Your Brazebee API key
  environment: 'https://api.brazebee.com'      // Required: Your Brazebee API URL
});
```

### Configuration Options

| Option | Type | Required | Description |
|--------|------|----------|-------------|
| `apiKeyAuth` | `string` | ✅ Yes | Your Brazebee API key |
| `environment` | `string` | ✅ Yes | Your Brazebee API base URL |

## Error Handling

```typescript
import { BrazebeeClient } from 'brazebee-sdk';

const client = new BrazebeeClient({
  apiKeyAuth: 'your_api_key',
  environment: 'https://api.brazebee.com'
});

try {
  await client.events.track({
    company: { id: 'acme_corp', name: 'Acme' },
    eventKey: 'feature_used'
  });
} catch (error: any) {
  if (error.statusCode === 400) {
    console.error('Bad Request:', error.message);
  } else if (error.statusCode === 401) {
    console.error('Unauthorized - Invalid API key');
  } else if (error.statusCode === 404) {
    console.error('Not Found - Event key not configured');
  } else if (error.statusCode === 429) {
    console.error('Rate limit exceeded');
  } else {
    console.error('Error:', error.message);
  }
}
```

## API Reference

### Track Event Request

```typescript
interface TrackEventRequest {
  company: CompanyInfo;      // Required: Company information
  user?: UserInfo;           // Optional: User information (omit for system events)
  eventKey: string;          // Required: Event key matching your configuration
  data?: Record<string, any>; // Optional: Custom event data
  timestamp?: Date;          // Optional: Event timestamp (defaults to now)
}
```

### Company Info

```typescript
interface CompanyInfo {
  id: string;                  // Required: Unique company identifier
  name: string;                // Required: Company name
  paymentProvider?: string;    // Optional: e.g., 'stripe', 'paddle'
  paymentProviderId?: string;  // Optional: Customer ID in payment system
  crmProvider?: string;        // Optional: e.g., 'salesforce', 'hubspot'
  crmProviderId?: string;      // Optional: Account ID in CRM
}
```

### User Info

```typescript
interface UserInfo {
  id: string;      // Required: Unique user identifier
  email: string;   // Required: User email
  name: string;    // Required: User name
}
```

### Field Naming Convention

**Important**: The TypeScript SDK uses **camelCase** for all fields:

| Correct (camelCase) ✅ | Incorrect (snake_case) ❌ |
|----------------------|--------------------------|
| `eventKey` | `event_key` |
| `paymentProvider` | `payment_provider` |
| `paymentProviderId` | `payment_provider_id` |
| `crmProvider` | `crm_provider` |
| `crmProviderId` | `crm_provider_id` |

### Complete Example

```typescript
import { BrazebeeClient } from 'brazebee-sdk';

const client = new BrazebeeClient({
  apiKeyAuth: process.env.BRAZEBEE_API_KEY!,
  environment: process.env.BRAZEBEE_API_URL!
});

// Example: Track onboarding completion with full details
await client.events.track({
  company: {
    id: 'acme_corp',
    name: 'Acme Corporation',
    paymentProvider: 'stripe',
    paymentProviderId: 'cus_MYwpG9X4kW7ZKi',
    crmProvider: 'salesforce',
    crmProviderId: '001500000ABCDEFGH'
  },
  user: {
    id: 'user_12345',
    email: 'john.doe@acme.com',
    name: 'John Doe'
  },
  eventKey: 'onboarding_completed',
  data: {
    steps_completed: 7,
    time_taken_seconds: 420,
    completion_rate: 100,
    plan: 'enterprise',
    user_role: 'admin'
  }
});
```

## Support

- **Documentation**: https://docs.brazebee.com
- **Dashboard**: https://app.brazebee.com
- **Email**: dev@brazebee.com
- **GitHub**: https://github.com/brazebee/brazebee-sdk-typescript

## License

Apache-2.0

