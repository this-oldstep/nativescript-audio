[![npm](https://img.shields.io/npm/v/nativescript-audio.svg)](https://www.npmjs.com/package/nativescript-audio)
[![npm](https://img.shields.io/npm/dt/nativescript-audio.svg?label=npm%20downloads)](https://www.npmjs.com/package/nativescript-audio)
[![Build Status](https://travis-ci.org/bradmartin/nativescript-audio.svg?branch=master)](https://travis-ci.org/bradmartin/nativescript-audio)
[![GitHub forks](https://img.shields.io/github/forks/bradmartin/nativescript-audio.svg)](https://github.com/bradmartin/nativescript-audio/network)
[![GitHub stars](https://img.shields.io/github/stars/bradmartin/nativescript-audio.svg)](https://github.com/bradmartin/nativescript-audio/stargazers)
[![nStudio Plugin](https://img.shields.io/badge/nStudio-Plugin-blue.svg)](http://nstudio.io)



# NativeScript-Audio

NativeScript plugin to play and record audio files for Android and iOS.

## Installation

The plugin is compatible with both Nativescript 3.x and 2.x versions. Install with:

`tns plugin add nativescript-audio`

---

### Android Native Classes

* [Player - android.media.MediaPlayer](http://developer.android.com/reference/android/media/MediaPlayer.html)
* [Recorder - android.media.MediaRecorder](http://developer.android.com/reference/android/media/MediaRecorder.html)

#### iOS Native Classes

* [Player - AVAudioPlayer](https://developer.apple.com/library/ios/documentation/AVFoundation/Reference/AVAudioPlayerClassReference/)
* [Recorder - AVAudioRecorder](https://developer.apple.com/library/ios/documentation/AVFoundation/Reference/AVAudioRecorder_ClassReference/)

Note: You will need to grant permissions on iOS to allow the device to access the microphone if you are using the recording function. If you don't, your app may crash on device and/or your app might be rejected during Apple's review routine. To do this, add this key to your `app/App_Resources/iOS/Info.plist` file:

```xml
<key>NSMicrophoneUsageDescription</key>
<string>Recording Practice Sessions</string>
```

## Usage

### TypeScript Example

```typescript
import { TNSPlayer } from "nativescript-audio";

export class YourClass {
  private _player: TNSPlayer;

  constructor() {
    this._player = new TNSPlayer();
    this._player.debug = true; // set true to enable TNSPlayer console logs for debugging.
    this._player
      .initFromFile({
        audioFile: "~/audio/song.mp3", // ~ = app directory
        loop: false,
        completeCallback: this._trackComplete.bind(this),
        errorCallback: this._trackError.bind(this)
      })
      .then(() => {
        this._player.getAudioTrackDuration().then(duration => {
          // iOS: duration is in seconds
          // Android: duration is in milliseconds
          console.log(`song duration:`, duration);
        });
      });
  }

  public togglePlay() {
    if (this._player.isAudioPlaying()) {
      this._player.pause();
    } else {
      this._player.play();
    }
  }

  private _trackComplete(args: any) {
    console.log("reference back to player:", args.player);
    // iOS only: flag indicating if completed succesfully
    console.log("whether song play completed successfully:", args.flag);
  }

  private _trackError(args: any) {
    console.log("reference back to player:", args.player);
    console.log("the error:", args.error);
    // Android only: extra detail on error
    console.log("extra info on the error:", args.extra);
  }
}
```

### Javascript Example:

```javascript
const audio = require("nativescript-audio");

const player = new audio.TNSPlayer();
const playerOptions = {
  audioFile: "http://some/audio/file.mp3",
  loop: false,
  completeCallback: function() {
    console.log("finished playing");
  },
  errorCallback: function(errorObject) {
    console.log(JSON.stringify(errorObject));
  },
  infoCallback: function(args) {
    console.log(JSON.stringify(args));
  }
};

player
  .playFromUrl(playerOptions)
  .then(function(res) {
    console.log(res);
  })
  .catch(function(err) {
    console.log("something went wrong...", err);
  });
```

## API

### Recorder

#### TNSRecorder Methods

| Method                                                      | Description                                               |
| ----------------------------------------------------------- | --------------------------------------------------------- |
| _TNSRecorder.CAN_RECORD()_: `boolean` - **_static method_** | Determine if ready to record.                             |
| _start(options: AudioRecorderOptions)_: `Promise<void>`     | Start recording to file.                                  |
| _stop()_: `Promise<void>`                                   | Stop recording.                                           |
| _pause()_: `Promise<void>`                                  | Pause recording.                                          |
| _resume()_: `Promise<void>`                                 | Resume recording.                                         |
| _dispose()_: `Promise<void>`                                | Free up system resources when done with recorder.         |
| _getMeters(channel?: number)_: `number`                     | Returns the amplitude of the input.                       |
| _isRecording()_: `boolean` - **_iOS Only_**                 | Returns true if recorder is actively recording.           |
| _requestRecordPermission()_: `Promise<void>`                | *Android Only* Resolves the promise is user grants the permission.       |
| _hasRecordPermission()_: `boolean`                          | *Android Only* Returns true if RECORD_AUDIO permission has been granted. |

#### TNSRecorder Instance Properties

| Property | Description                                                |
| -------- | ---------------------------------------------------------- |
| ios      | Get the native AVAudioRecorder class instance.             |
| android  | Get the native MediaRecorder class instance.               |
| debug    | Set true to enable debugging console logs (default false). |

### Player

#### TNSPlayer Methods

| Method                                                                 | Description                                                  |
| ---------------------------------------------------------------------- | ------------------------------------------------------------ |
| _initFromFile(options: AudioPlayerOptions)_: `Promise`                 | Initialize player instance with a file without auto-playing. |
| _playFromFile(options: AudioPlayerOptions)_: `Promise`                 | Auto-play from a file.                                       |
| _initFromUrl(options: AudioPlayerOptions)_: `Promise`                  | Initialize player instance from a url without auto-playing.  |
| _playFromUrl(options: AudioPlayerOptions)_: `Promise`                  | Auto-play from a url.                                        |
| _pause()_: `Promise<boolean>`                                          | Pause playback.                                              |
| _resume()_: `void`                                                     | Resume playback.                                             |
| _seekTo(time:number)_: `Promise<boolean>`                              | Seek to position.                                            |
| _dispose()_: `Promise<boolean>`                                        | Free up resources when done playing audio.                   |
| _isAudioPlaying()_: `boolean`                                          | Determine if player is playing.                              |
| _getAudioTrackDuration()_: `Promise<string>`                           | Duration of media file assigned to the player.               |
| _playAtTime(time: number)_: void - **_iOS Only_**                      | Play audio track at specific time of duration.               |
| _changePlayerSpeed(speed: number)_: void - **On Android Only API 23+** | Change the playback speed of the media player.               |

#### TNSPlayer Instance Properties

| Property                | Description                                                |
| ----------------------- | ---------------------------------------------------------- |
| _ios_                   | Get the native ios AVAudioPlayer instance.                 |
| _android_               | Get the native android MediaPlayer instance.               |
| _debug_: `boolean`      | Set true to enable debugging console logs (default false). |
| _currentTime_: `number` | Get the current time in the media file's duration.         |
| _volume_: `number`      | Get/Set the player volume. Value range from 0 to 1.        |

## License

[MIT](/LICENSE)
