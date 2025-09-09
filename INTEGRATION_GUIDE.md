# TradrLab Widget Integration Guide

## Overview

The TradrLab Widget is a fully functional, embeddable AI-powered trading strategy development interface. It provides complete integration with TradrLab's backend services for creating, testing, and analyzing systematic trading strategies through natural language conversation.

### Key Features
- **AI-Powered Chat Interface**: Natural language conversation for strategy development
- **Real-time Communication**: SignalR integration for instant AI responses
- **Complete Backend Integration**: Automatic loading of trading symbols, templates, and analysis blocks
- **JWT Authentication**: Secure API key-based authentication with automatic user management
- **Two User Journey Modes**: Interactive exploration and template-based development
- **Production Ready**: Handles all edge cases, errors, and rate limiting

### User Journey Modes

1. **Interactive Mode**: Open-ended conversation to explore and develop custom trading strategies
   - User describes strategy ideas in natural language
   - AI helps refine concepts into logical steps (Milestone 1: Scenario)  
   - System generates executable trading model (Milestone 2: Model)
   - Model gets evaluated against market data (Milestone 3: Execution)
   - User can ask questions about results (Milestone 4: Analysis)
   - Optional backtesting available (Milestone 5: Backtesting)

2. **Template Mode**: Quick-start with pre-built strategy templates
   - User selects from library of proven strategy templates
   - System automatically progresses through Milestones 1-3
   - User continues with analysis and backtesting

## Prerequisites

Before integrating the widget, you must:

1. **Obtain API Key**: Apply for a widget API key from TradrLab (contact support@tradrlab.com)
2. **User Management**: Implement unique external user IDs for each of your users
3. **HTTPS Environment**: Widget requires HTTPS for secure API communication
4. **CORS Configuration**: Ensure your domain is allowed by TradrLab's CORS policy

## Architecture Overview

### Authentication Flow
1. Widget validates your API key with TradrLab's servers
2. System creates or retrieves user account using your external user ID
3. Widget receives JWT token for authenticated API access
4. All subsequent API calls and SignalR connections use the JWT token

### Required API Endpoints
The widget automatically connects to these TradrLab API endpoints:

- **`/api/widget/auth/token`** - JWT token generation for authentication
- **`/api/widget/blocks`** - Trading analysis blocks and templates
- **`/candlechart/data/currencies`** - Available trading symbols and timeframes
- **`/api/hubs/message`** - SignalR hub for real-time AI communication
- **`/api/widget/chat`** - Chat message processing
- **`/api/widget/query`** - Interactive mode scenario generation
- **`/api/widget/execute`** - Strategy execution and backtesting

## Quick Start

### 1. Include the Widget Script

Download the widget files and include the script in your HTML:

```html
<script src="widget-core.js"></script>
```

### 2. Basic Integration

```html
<!DOCTYPE html>
<html>
<head>
    <title>TradrLab Widget Integration</title>
</head>
<body>
    <div id="widget-container"></div>

    <script src="widget-core.js"></script>
    <script>
        // Configure global settings
        window.TradrLabWidget.globals({
            apikey: 'YOUR_API_KEY_HERE',  // Required: Your TradrLab API key
            apiBaseUrl: 'https://dev.api.tradrlab.com/api',
            debug: true  // Set to false in production
        });

        // Create widget instance
        const widget = window.TradrLabWidget.CreateWidget('chat', 
            document.getElementById('widget-container'), {
            
            // Required: Unique identifier for your user
            externalUserId: 'user-123',
            
            // Optional: Widget appearance
            theme: 'light',
            width: '100%',
            height: '100%',
            
            // Optional: Event callbacks
            onWidgetReady: function() {
                console.log('Widget ready!');
            },
            onMessageReceived: function(message) {
                console.log('AI Response:', message);
            },
            onError: function(error) {
                console.error('Widget error:', error);
            }
        });
    </script>
</body>
</html>
```

## API Reference

### Global Configuration

#### `TradrLabWidget.globals(config)`

Configure global settings that apply to all widget instances.

**Parameters:**
- `config` (Object): Global configuration
  - `apikey` (String, **required**): Your TradrLab widget API key
  - `apiBaseUrl` (String, optional): API base URL (default: 'https://dev.api.tradrlab.com/api')
  - `debug` (Boolean, optional): Enable debug logging (default: false)

**Example:**
```javascript
window.TradrLabWidget.globals({
    apikey: 'wapi_your_key_here',
    apiBaseUrl: 'https://dev.api.tradrlab.com/api',
    debug: true  // Enable for development
});
```

### Widget Creation

#### `TradrLabWidget.CreateWidget(type, targetElement, options)`

Create a new widget instance.

**Parameters:**
- `type` (String): Widget type - currently only `'chat'` is supported
- `targetElement` (HTMLElement): DOM element where widget will render
- `options` (Object): Widget configuration

**Required Options:**
- `externalUserId` (String): Unique identifier for your user

**Optional Options:**
- `theme` (String): 'light' or 'dark' (default: 'light')
- `width` (String): Widget width (default: '100%')
- `height` (String): Widget height (default: '600px')
- `placeholder` (String): Input placeholder text
- Event callbacks (see Event Handling section)

**Returns:** Widget instance object

### Event Handling

The widget provides comprehensive event callbacks for monitoring user interactions and AI responses:

#### `onWidgetReady(widgetInstance)`
Called when widget is fully initialized and ready for use.

```javascript
onWidgetReady: function(widget) {
    console.log('Widget initialized with user:', widget.config.get('userId'));
}
```

#### `onMessageReceived(message)`
Called when AI sends a response. Message types include:

- `Chat` - General conversation responses
- `Scenario` - Strategy scenario descriptions (Interactive Mode Milestone 1)
- `Model` - Generated trading models (Milestone 2) 
- `ExecutionResult` - Strategy execution results (Milestone 3)
- `Template` - Template-based strategy responses (Template Mode)
- `Error` - Error messages and warnings

```javascript
onMessageReceived: function(message) {
    console.log(`AI Response (${message.messageType}):`, message.content);
    
    // Handle different message types
    switch(message.messageType) {
        case 'Scenario':
            console.log('Strategy scenario ready for model generation');
            break;
        case 'Model': 
            console.log('Trading model generated:', message);
            break;
        case 'ExecutionResult':
            console.log('Strategy execution completed:', message);
            break;
    }
}
```

#### `onError(error)`
Called when errors occur during widget operation.

```javascript
onError: function(error) {
    console.error('Widget error:', error);
    // Implement your error handling logic
}
```

#### `onSignalRConnected()`
Called when real-time SignalR connection is established.

```javascript
onSignalRConnected: function() {
    console.log('Real-time connection established');
}
```

### Widget Instance Methods

#### `widget.sendMessage(content)`
Send a message programmatically.

```javascript
widget.sendMessage('Create a momentum strategy for EURUSD');
```

#### `widget.config.get(key)` / `widget.config.set(key, value)`
Get or set widget configuration values.

```javascript
const currentSymbol = widget.config.get('defaultSymbol');
widget.config.set('defaultSymbol', 'GBPUSD');
```

#### `widget.destroy()`
Clean up and remove the widget.

```javascript
widget.destroy();
```

## Advanced Integration

### Multiple Users

For applications with multiple users, ensure each has a unique `externalUserId`:

```javascript
// Example: Using your application's user system
const currentUser = getCurrentUser(); // Your user management function

const widget = window.TradrLabWidget.CreateWidget('chat', container, {
    externalUserId: `user-${currentUser.id}`,
    // ... other options
});
```

### Custom Styling

Override widget styles by targeting CSS classes:

```css
/* Widget container customization */
.tradrlab-widget {
    border: 2px solid #your-color;
    border-radius: 12px;
    font-family: 'Your Font', sans-serif;
}

/* Chat message customization */  
.tradrlab-widget .chat-message {
    background-color: #your-background;
}
```

### Error Handling

Implement comprehensive error handling for production use:

```javascript
const widget = window.TradrLabWidget.CreateWidget('chat', container, {
    externalUserId: 'user-123',
    onError: function(error) {
        console.error('TradrLab Widget Error:', error);
        
        // Handle specific error types
        switch(error.type) {
            case 'API_KEY_INVALID':
                showUserMessage('Invalid API key. Please contact support.');
                break;
            case 'RATE_LIMIT_EXCEEDED':
                showUserMessage('Rate limit exceeded. Please wait before trying again.');
                break;
            case 'NETWORK_ERROR':
                showUserMessage('Connection error. Please check your internet connection.');
                break;
            default:
                showUserMessage('An error occurred. Please try again.');
        }
    }
});
```

### Programmatic Interaction

Interact with the widget programmatically:

```javascript
// Send predefined strategy requests
document.getElementById('momentum-btn').onclick = () => {
    widget.sendMessage('Create a momentum strategy using RSI and moving averages');
};

// Update trading settings
document.getElementById('symbol-select').onchange = (e) => {
    widget.config.set('defaultSymbol', e.target.value);
    widget.sendMessage(`Please analyze strategies for ${e.target.value}`);
};
```

## User Journey Examples

### Interactive Mode Flow

```javascript
const widget = window.TradrLabWidget.CreateWidget('chat', container, {
    externalUserId: 'user-123',
    onMessageReceived: function(message) {
        switch(message.messageType) {
            case 'Scenario':
                // Milestone 1: AI understood user's strategy idea
                showProgress('Strategy concept defined ✅');
                break;
            case 'Model':
                // Milestone 2: Executable model created  
                showProgress('Trading model generated ✅');
                break;
            case 'ExecutionResult':
                // Milestone 3: Model executed against market data
                showProgress('Strategy evaluated ✅');
                showResults(message);
                break;
        }
    }
});

// User starts conversation
widget.sendMessage("I want to create a breakout strategy that buys when price breaks above resistance");
```

### Template Mode Flow

```javascript
const widget = window.TradrLabWidget.CreateWidget('chat', container, {
    externalUserId: 'user-123',
    onWidgetReady: function(widget) {
        // Widget automatically loads available templates
        // User can browse and select templates through the UI
        console.log('Templates loaded and ready for selection');
    },
    onMessageReceived: function(message) {
        if (message.messageType === 'Template') {
            console.log('Template-based strategy loaded:', message.templateName);
        }
    }
});
```

## Data Integration

### Symbol and Timeframe Data

The widget automatically loads available trading symbols and timeframes from TradrLab's database:

- **116+ Trading Symbols**: Forex pairs, indices, commodities, cryptocurrencies
- **Multiple Timeframes**: 1M, 5M, 15M, 30M, 1H, 4H, 1D
- **Real-time Data**: Updated market data for strategy testing

### Template Library

Access to TradrLab's curated template library:

- **Trend Following Strategies**: Moving averages, breakouts, momentum
- **Mean Reversion Strategies**: RSI, Bollinger Bands, support/resistance
- **Pattern Recognition**: Candlestick patterns, technical formations
- **Economic Strategies**: News-based and fundamental analysis approaches

## Security Considerations

### API Key Management

- **Never expose API keys** in client-side code for production
- **Use environment variables** or secure configuration management
- **Implement rate limiting** in your application
- **Monitor API usage** through TradrLab's dashboard

### User Data Protection

- **Use non-guessable external user IDs** 
- **Don't include sensitive information** in user IDs
- **Implement proper session management** in your application
- **Follow data privacy regulations** for your jurisdiction

## Troubleshooting

### Widget Not Loading
- Verify script path is correct and accessible
- Check browser console for JavaScript errors
- Ensure HTTPS is being used
- Verify API key is valid and not expired

### Authentication Issues  
- Check that API key is correctly formatted
- Ensure `externalUserId` is provided and unique
- Verify your domain is allowed in CORS settings
- Check browser Network tab for authentication errors

### SignalR Connection Problems
- Verify WebSocket connections are allowed through firewalls
- Check that SignalR hub URL is accessible  
- Ensure JWT token is being generated correctly
- Monitor browser console for SignalR debugging information

### No AI Responses
- Verify SignalR connection is established (check `onSignalRConnected` callback)
- Ensure messages are being sent successfully
- Check for rate limiting or quota exceeded errors
- Monitor TradrLab API status

## API Limits and Quotas

Different API endpoints have different rate limits:

- **Chat Messages**: 100 requests per minute per user
- **DAG Generation**: 10 requests per minute per user  
- **Data Queries**: 50 requests per minute per user
- **Template Access**: 20 requests per minute per user

Rate limits are enforced per API key and tracked per external user ID.

## Support and Resources

### Getting Help
- **Email**: support@tradrlab.com
- **Documentation**: https://docs.tradrlab.com
- **API Status**: https://status.tradrlab.com
- **GitHub Issues**: https://github.com/tradrlab/widget/issues

### Example Code
- **Basic Integration**: See `integration-example.html` in this repository
- **Complete Demo**: The integration example shows simplified widget setup
- **React Integration**: Contact support for React-specific examples
- **Vue/Angular**: Framework-specific integration guides available

### API Reference
- **Widget API**: Complete endpoint documentation at https://dev.api.tradrlab.com/api/swagger
- **WebSocket Events**: Real-time event documentation  
- **Error Codes**: Comprehensive error code reference

## Changelog

### Version 1.0.0 (Current)
- ✅ Initial release with complete chat functionality
- ✅ JWT authentication and user management
- ✅ Interactive and Template mode support  
- ✅ Real-time SignalR communication
- ✅ Complete API integration
- ✅ Production-ready error handling

### Roadmap
- **Advanced Analytics**: Enhanced backtesting and performance metrics
- **Custom Templates**: Allow creation of custom strategy templates
- **Multi-language Support**: Internationalization capabilities
- **Mobile Optimization**: Enhanced mobile device support

## License

The TradrLab Widget is proprietary software. Usage requires a valid API key and agreement to TradrLab's Terms of Service. Contact legal@tradrlab.com for licensing information.