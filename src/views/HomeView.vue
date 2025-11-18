<script setup lang="ts">
import AgoraRTC from 'agora-rtc-sdk-ng'

// RTC client instance
let client = null
// Local audio track
let localAudioTrack = null

// Connection parameters
const appId = '5bb31a5684af47a1aff766e1c00b6de2'
const channel = 'testc'
const token =
  '007eJxTYNjj8uLwusZS4zdLV12f2uYwPffCl+SCum2/pA38Pj7jPH5cgcE0KcnYMNHUzMIkMc3EPNEwMS3N3Mws1TDZwCDJLCXVqDVVJrMhkJFB9+9NJkYGCATxWRlKUotLkhkYAIKFI1k='
const uid = 0 // User ID

// Initialize the AgoraRTC client
function initializeClient() {
  client = AgoraRTC.createClient({ mode: 'rtc', codec: 'vp8' })
  setupEventListeners()
}

// Handle client events
function setupEventListeners() {
  // Set up event listeners for remote tracks
  client.on('user-published', async (user, mediaType) => {
    // Subscribe to the remote user when the SDK triggers the "user-published" event
    await client.subscribe(user, mediaType)
    console.log('subscribe success')
    // If the remote user publishes an audio track.
    if (mediaType === 'audio') {
      // Get the RemoteAudioTrack object in the AgoraRTCRemoteUser object.
      const remoteAudioTrack = user.audioTrack
      // Play the remote audio track.
      remoteAudioTrack.play()
    }
  })

  // Listen for the "user-unpublished" event
  client.on('user-unpublished', async (user) => {
    // Remote user unpublished
  })
}

// Create a local audio track
async function createLocalAudioTrack() {
  localAudioTrack = await AgoraRTC.createMicrophoneAudioTrack()
}

// Join the channel and publish local audio
async function joinChannel() {
  await client.join(appId, channel, token, uid)
  await createLocalAudioTrack()
  await publishLocalAudio()
  console.log('Publish success!')
}

// Publish local audio track
async function publishLocalAudio() {
  await client.publish([localAudioTrack])
}

// Leave the channel and clean up
async function leaveChannel() {
  localAudioTrack.close() // Stop local audio
  await client.leave() // Leave the channel
  console.log('Left the channel.')
}

// Set up button click handlers
function setupButtonHandlers() {
  document.getElementById('join').onclick = joinChannel
  document.getElementById('leave').onclick = leaveChannel
}

// Start the basic call process
function startBasicCall() {
  initializeClient()
  window.onload = setupButtonHandlers
}

startBasicCall()
</script>

<template>
  <main>
    <h2 class="left-align">Agora Web SDK Quickstart</h2>
    <div class="row">
      <div>
        <button type="button" id="join">Join</button>
        <button type="button" id="leave">Leave</button>
      </div>
    </div>
  </main>
</template>
