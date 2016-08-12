---
layout: post
title:  "Capturing audio the native way - Android"
date:   2016-08-12
description: "The android platform has a very easy way that we could capture audio; either from the gadgets microphone, or a call. Lets see how we will build a custom recording application."
tag: [mobile, audio-capture, java, native-apps]
categories: [android]
share: true
comments: true
project: false
---

The Android multimedia framework includes support for capturing and encoding a variety of common audio formats, so that you can easily integrate audio into your applications. You can record audio using the [MediaRecorder](https://developer.android.com/reference/android/media/MediaRecorder.html) APIs if supported by the device hardware and playback audio using [MediaPlayer](https://developer.android.com/reference/android/media/MediaPlayer.html). This write-up will introduce you on how to record audio and play it back.

> #### Note: The Android Emulator does not have the ability to capture audio, but actual devices are likely to provide these capabilities.

<!-- more -->

You will need the following tools to accomplish the purpose of this write-up:

* An android phone (for testing)
* Android studio. [Download and install for your platform.](https://developer.android.com/studio/index.html)
* Time
* Game-on attitude

## Starting a new application

To start a new application in android studio do the follow the images below.

{% capture images %}
	/images/posts/studio1.png
	/images/posts/audio_capture_1.png
	/images/posts/audio_capture_2.png
	/images/posts/audio_capture_3.png
	/images/posts/audio_capture_4.png
	/images/posts/audio_capture_5.png
{% endcapture %}
{% include gallery images=images caption="" cols=3 %}

Basically we are starting off with an empty activity template. What this means is that we are given the whole authority to choose which ever way we want our launcher (first activity that shows) to be.

## Designing the view

Enough of the talks. Lets do some action. We will start off first with the view of the application. We need to put our designing swag on as this will need a little bit of artistic mind to finish it up. Okay, I came up with the following design.

{% highlight xml %}
    <?xml version="1.0" encoding="utf-8"?>
    <RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:paddingBottom="1dp"
        android:paddingLeft="2dp"
        android:paddingRight="2dp"
        android:paddingTop="1dp"
        tools:context="me.nenepadi.nene.audiocapture.MainActivity">


        <LinearLayout
            android:orientation="vertical"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:layout_alignParentTop="true"
            android:layout_centerHorizontal="true">

            <TextView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:textAppearance="?android:attr/textAppearanceLarge"
                android:text=""
                android:id="@+id/textView"
                android:layout_gravity="center_horizontal"/>

            <ImageView
                android:layout_width="wrap_content"
                android:layout_height="0dp"
                android:id="@+id/imageView"
                android:layout_gravity="center_horizontal"
                android:src="@drawable/mic"
                android:layout_weight="1" />

            <RelativeLayout
                android:orientation="horizontal"
                android:layout_width="match_parent"
                android:layout_height="wrap_content">

                <Button
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="play record"
                    android:id="@+id/btnPlayRecord"
                    android:layout_alignParentLeft="true"
                    android:layout_alignParentStart="true"
                    android:layout_gravity="center_horizontal"
                    android:onClick="audioCaptureAndPlayback"
                    android:enabled="false" />

                <Button
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="refresh rec"
                    android:id="@+id/btnRefreshActivity"
                    android:layout_centerInParent="true"
                    android:layout_gravity="center_horizontal"
                    android:visibility="invisible"
                    android:onClick="audioCaptureAndPlayback"/>

                <Button
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="start record"
                    android:layout_centerInParent="true"
                    android:id="@+id/btnStartRecord"
                    android:layout_gravity="center_horizontal"
                    android:onClick="audioCaptureAndPlayback"/>

                <Button
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="stop record"
                    android:layout_alignParentRight="true"
                    android:layout_alignParentEnd="true"
                    android:id="@+id/btnStopRecord"
                    android:layout_gravity="center_horizontal"
                    android:onClick="audioCaptureAndPlayback"
                    android:enabled="false" />

                <Button
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="stop playbk"
                    android:layout_alignParentRight="true"
                    android:layout_alignParentEnd="true"
                    android:id="@+id/btnStopPlayback"
                    android:layout_gravity="center_horizontal"
                    android:visibility="invisible"
                    android:onClick="audioCaptureAndPlayback"/>
            </RelativeLayout>

        </LinearLayout>
    </RelativeLayout>
{% endhighlight %}

This should be generating a layout that looks like this:

<figure class="half center">
    <img src="/images/posts/audio_capture_6.png" alt="">
    <figcaption>activity_main.xml result</figcaption>
</figure>

## Making the app work

Now lets outline the steps that the app will go through to call it a working application.

1. Launch the app.
2. Click the __START RECORD__ button and record your sound.
3. Eventually click the __STOP RECORD__ button to stop recording.
4. Click the __PLAY RECORD__ button to play the sound you recorded.
5. Eventually click the __STOP PLAYBK__ to stop the playback.
6. Click the __REFRESH REC__ to start the process over again.

Simple right? Lets take this little by little. First lets see how our _MainActivity.java_ file should look like before we start editing it.

{% highlight java %}
    package me.nenepadi.nene.audiocapture;

    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    import android.view.View;

    public class MainActivity extends AppCompatActivity {

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
        }

        public void audioCaptureAndPlayback(View view) {
        }
    }
{% endhighlight %}

Now lets create an instance of all the elements on the view and every other variables we might need.

{% highlight java %}
    .....
    .....
    .....

    import android.annotation.SuppressLint;
    import android.content.Intent;
    import android.media.MediaPlayer;
    import android.media.MediaRecorder;
    import android.os.Environment;
    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    import android.view.View;
    import android.widget.Button;
    import android.widget.ImageView;
    import android.widget.TextView;

    import java.io.File;
    import java.io.IOException;

    public class MainActivity extends AppCompatActivity {

        Button btnPlayRecord, btnRefreshActivity, btnStartRecord,
                btnStopRecord, btnStopPlayback;
        TextView tvStatus;
        ImageView ivPicture;
        Intent intent;

        private MediaRecorder mediaRecorder;
        private MediaPlayer mediaPlayer;
        private String OUT_FILE;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            // set the output file insite the external storage area
            OUT_FILE = Environment.getExternalStorageDirectory() +
                    "/audioCaptureSound.3gpp";

            // use the id reference of all the elements to create java-like gui objects
            btnPlayRecord = (Button) findViewById(R.id.btnPlayRecord);
            btnRefreshActivity = (Button) findViewById(R.id.btnRefreshActivity);
            btnStartRecord = (Button) findViewById(R.id.btnStartRecord);
            btnStopRecord = (Button) findViewById(R.id.btnStopRecord);
            btnStopPlayback = (Button) findViewById(R.id.btnStopPlayback);

            tvStatus = (TextView) findViewById(R.id.textView);
            ivPicture = (ImageView) findViewById(R.id.imageView);
        }

        .....
        .....
        .....
    }
{% endhighlight %}

Now lets go on and write some code for the one function/method that controls all our major actions or events.

{% highlight java %}
    .....
    .....
    .....

    public class MainActivity extends AppCompatActivity {

        .....
        .....
        .....

        public void audioCaptureAndPlayback(View view) {
            switch (view.getId()){
                case R.id.btnStartRecord:
                    try{
                        startRecord();
                    } catch(Exception e){
                        e.printStackTrace();
                    }
                    break;
                case R.id.btnStopRecord:
                    try{
                        stopRecord();
                    } catch(Exception e){
                        e.printStackTrace();
                    }
                    break;
                case R.id.btnPlayRecord:
                    try{
                        playRecord();
                    } catch(Exception e){
                        e.printStackTrace();
                    }
                    break;
                case R.id.btnStopPlayback:
                    try{
                        stopPlayback();
                    } catch(Exception e){
                        e.printStackTrace();
                    }
                    break;
                case R.id.btnRefreshActivity:
                    try{
                        refreshActivity();
                    } catch(Exception e){
                        e.printStackTrace();
                    }
                    break;
                default:
                    break;
            }
        }

        .....
        .....
        .....
    }
{% endhighlight %}

To record a sound you have to do the following:

1. set the source of the recording to the phone's microphone
2. make output format .3gpp
3. encode the recorded file
4. set the output file
5. prepare and start recording

This is how the _startRecord()_ function should look like:

{% highlight java %}
    @SuppressLint("SetTextI18n")  // used to suppress internationalisation of texts ...
    private void startRecord() throws IOException {
        // release mediaRecorder if the is one trending already ...
        if(mediaRecorder != null)
            mediaRecorder.release();

        // create a file to test whether one exists already. Delete if it exists ...
        File output_file = new File(OUT_FILE);
        if(output_file.exists())
            output_file.delete();

        // disable btnStartRecord and enable btnStopRecord
        btnStartRecord.setVisibility(View.INVISIBLE);
        btnRefreshActivity.setVisibility(View.VISIBLE);
        btnStopRecord.setEnabled(true);
        btnPlayRecord.setEnabled(false);

        /*
         * 1. set the source of the recording to the phone's microphone
         * 2. make output format .3gpp
         * 3. encode the recorded file
         * 4. set the output file
         * 5. prepare and start recording
         */
        mediaRecorder = new MediaRecorder();
        mediaRecorder.setAudioSource(MediaRecorder.AudioSource.MIC);
        mediaRecorder.setOutputFormat(MediaRecorder.OutputFormat.THREE_GPP);
        mediaRecorder.setAudioEncoder(MediaRecorder.AudioEncoder.AMR_NB);
        mediaRecorder.setOutputFile(OUT_FILE);
        mediaRecorder.prepare();
        mediaRecorder.start();

        tvStatus.setText("Audio is been recorded ...");
    }
{% endhighlight %}

This is how the _stopRecord()_ function should look like:

{% highlight java %}
    @SuppressLint("SetTextI18n")
    private void stopRecord() {
        // stop mediaRecorder
        if(mediaRecorder != null)
            mediaRecorder.stop();

        // make visible the playback buttons ...
        btnPlayRecord.setEnabled(true);
        btnStopRecord.setEnabled(false);

        tvStatus.setText("Audio saved successfully ...");

        // change the inageviews source using a resource id ...
        ivPicture.setImageResource(R.drawable.headphones);
    }
{% endhighlight %}

This is how the _playRecord()_ should look like:

{% highlight java %}
    @SuppressLint("SetTextI18n")
    private void playRecord() throws IOException {
        // ditch mediaPlayer if it is already active ...
        if(mediaPlayer != null)
            mediaPlayer.release();

        btnPlayRecord.setEnabled(false);
        btnStopRecord.setVisibility(View.INVISIBLE);
        btnStopPlayback.setVisibility(View.VISIBLE);
        btnStopPlayback.setEnabled(true);

        // set media source, prepare and play it ... 
        mediaPlayer = new MediaPlayer();
        mediaPlayer.setDataSource(OUT_FILE);
        mediaPlayer.prepare();
        mediaPlayer.start();
        tvStatus.setText("Playing recorded sound ...");
    }
{% endhighlight %}

This is how the _stopPlayback()_ should look like:

{% highlight java %}
    @SuppressLint("SetTextI18n")
    private void stopPlayback() {
        if(mediaPlayer != null){
            try{
                mediaPlayer.release();
            } catch(Exception e){
                e.printStackTrace();
            }
        }

        btnPlayRecord.setEnabled(true);
        btnStopPlayback.setEnabled(false);

        tvStatus.setText("Playback stopped successfully ...");
    }
{% endhighlight %}

This is how the _refreshActivity()_ should look like:

{% highlight java %}
    private void refreshActivity() {
        intent = new Intent(MainActivity.this, MainActivity.class);
        overridePendingTransition(0, 0);
        intent.addFlags(Intent.FLAG_ACTIVITY_NO_ANIMATION);
        finish();
        overridePendingTransition(0, 0);
        startActivity(intent);
    }
{% endhighlight %}

Now finally, to make the application not hang when recording sound we need to seek permission from the device to use it's microphone and also gain access to write a file to the external storage. All these should be done in the _AndroidManifest.xml_ file. This is how this file should look like:

{% highlight xml %}
    <?xml version="1.0" encoding="utf-8"?>
    <manifest xmlns:android="http://schemas.android.com/apk/res/android"
        package="me.nenepadi.nene.audiocapture">

        <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
        <uses-permission android:name="android.permission.RECORD_AUDIO" />

        <application
            android:allowBackup="true"
            android:icon="@mipmap/ic_launcher"
            android:label="@string/app_name"
            android:supportsRtl="true"
            android:theme="@style/AppTheme"
            android:screenOrientation="portrait">
            <activity android:name=".MainActivity">
                <intent-filter>
                    <action android:name="android.intent.action.MAIN" />

                    <category android:name="android.intent.category.LAUNCHER" />
                </intent-filter>
            </activity>
        </application>

    </manifest>
{% endhighlight %}

Note that all extra drawables were downloaded. The [headphones drawable](http://iconizer.net/files/Brilliant/orig/headphones.png) and the [microphone drawable](http://images.clipartpanda.com/microphone-black-and-white-audio_input_microphone_coloring_book-999px.png).
{: .notice}

After you are sure you understand the processes taken to record a media file and play it back, test the application on a physical device - not an emulator, and __leave your issues and feedback in the comments box__. Whoops this is lengthy, but it is worthwhile.

Ardios.