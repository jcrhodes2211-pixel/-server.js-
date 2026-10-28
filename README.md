const http = require('http');
const WebSocket = require('ws');

const SERVER_PORT = process.env.PORT || 8080;

// Initialize Standard HTTP Base Layer
const server = http.createServer((request, response) => {
    response.writeHead(200, { 'Content-Type': 'text/plain' });
    response.end('Decipher Cue Telemetry Pipeline Core Active\n');
});

// Bind WebSocket Server Instance for Real-Time Full-Duplex Lookups
const wss = new WebSocket.Server({ server });

wss.on('connection', (socket, req) => {
    const originIp = req.socket.remoteAddress;
    console.log(`[TELEMETRY PIPELINE CONNECTED]: Client verified from route link node: ${originIp}`);

    socket.on('message', (messagePacket) => {
        try {
            const parsedData = JSON.parse(messagePacket);
            console.log(`[RX STREAM RECEIVED]: Processing active packet: ${parsedData.action}`);
            
            // Replicates real-time search update delivery parameters
            if (parsedData.action === 'QUERY_PARTICIPANTS') {
                const feedbackResponse = {
                    event: "SEARCH_RESULTS_UPDATE",
                    data: {
                        results: [
                            {
                                display_name: "Student Profile 02",
                                role_type: "STUDENT",
                                assigned_node: "Classroom Node Alpha",
                                clearance_status: "KIDS_SAFE_RESTRICTED"
              }
                        ]
                    }
                };
                socket.send(JSON.stringify(feedbackResponse));
                console.log(`[TX DISPATCHED]: Search response packet emitted down the data wire.`);
            }
        } catch (fault) {
            console.error('[PARSING EXCEPTION]: Packet failed baseline schema constraints.', fault.message);
        }
    });

    socket.on('close', () => {
        console.log('[TELEMETRY DISCONNECTED]: Client socket line severed cleanly.');
    });
});

// Initialize Listener Loop
server.listen(SERVER_PORT, () => {
    console.log(`================================================================================`);
    console.log(`🚀 [TEST/BETA BOOTED]: Telemetry Router microservice active on port ${SERVER_PORT}`);
    console.log(`================================================================================`);
});
