# app
android-app


Button btnStartRecord, btnPause, btnStopRecord, btnPlay, btnStop, btnSendToEmails;
    String pathSave = "";
    MediaRecorder mediaRecorder;
    MediaPlayer mediaPlayer;
    Random random ;

    String RandomAudioFileName = "ABCDEFGHIJKLMNOP";
    final int REQUEST_PERMISSION_CODE = 1000;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main_activity_layout);

        btnSendToEmails = (Button) findViewById(R.id.btnSendToEmails);
        random = new Random();
        
        btnSendToEmails.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                final String address = ContactsContract.CommonDataKinds.Email.ADDRESS;
            }
        });

        if(!checkPermissionFromDevice())
            requestPermissions();

        btnPlay = (Button)findViewById(R.id.btnPlayRecord);
        btnPause = (Button)findViewById(R.id.btnPause);
        btnStartRecord = (Button) findViewById(R.id.btnStartRecord);
        btnStop = (Button)findViewById(R.id.btnStopPlayRecord);
        btnStopRecord = (Button)findViewById(R.id.btnStopRecord);

            btnStartRecord.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View v) {
                    if(checkPermissionFromDevice())
                    {

                    pathSave = Environment.getExternalStorageDirectory()
                            .getAbsolutePath()+'/'
                            + UUID.randomUUID().toString()+"_audio_record.3gp";
                    setupMediaRecorder();
                    try{
                        mediaRecorder.prepare();
                        mediaRecorder.start();
                    } catch (IOException e) {
                        e.printStackTrace();
                    }

                    btnPlay.setEnabled(false);
                    btnStop.setEnabled(false);
                    btnPause.setEnabled(true);

                    Toast.makeText(MainActivity.this, "Recording...", Toast.LENGTH_SHORT).show();
                    }
                    else
                    {
                        requestPermissions();
                    }
                }
            });


            btnStopRecord.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View v) {
                     mediaRecorder.stop();
                     btnStopRecord.setEnabled(false);
                     btnPlay.setEnabled(true);
                     btnStartRecord.setEnabled(true);
                     btnStop.setEnabled(false);
                     btnPause.setEnabled(false);
                }
            });


            btnPause.setOnClickListener(new View.OnClickListener() {
                @RequiresApi(api = Build.VERSION_CODES.N)
                @Override
                public void onClick(View v) {
                     Toast.makeText(getApplicationContext(), "Pausing record",Toast.LENGTH_LONG).show();
                     mediaPlayer.pause();
                     btnStopRecord.setEnabled(false);
                     btnPlay.setEnabled(true);
                     btnStartRecord.setEnabled(true);
                     btnStop.setEnabled(false);
                     btnPause.setEnabled(false);

                }
            });


            btnPlay.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View v) {
                    btnStop.setEnabled(true);
                    btnStopRecord.setEnabled(false);
                    btnStartRecord.setEnabled(false);
                    btnPause.setEnabled(true);

                    mediaPlayer = new MediaPlayer();
                    try {
                        mediaPlayer.setDataSource(pathSave);
                        mediaPlayer.prepare();
                    } catch (IOException e) {
                        e.printStackTrace();
                    }

                    mediaPlayer.start();
                    Toast.makeText(MainActivity.this, "Playing...",
                            Toast.LENGTH_SHORT).show();
                }
            });


            btnStop.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View v) {
                    btnStopRecord.setEnabled(false);
                    btnStartRecord.setEnabled(true);
                    btnStop.setEnabled(false);
                    btnPlay.setEnabled(true);
                    btnPause.setEnabled(false);

                    if(mediaPlayer != null)
                    {
                        mediaPlayer.stop();
                        mediaPlayer.release();
                        setupMediaRecorder();
                    }
                }
            });
        }



    private void setupMediaRecorder() {
        mediaRecorder = new MediaRecorder();
        mediaRecorder.setAudioSource(MediaRecorder.AudioSource.MIC);
        mediaRecorder.setOutputFormat(MediaRecorder.OutputFormat.THREE_GPP);
        mediaRecorder.setAudioEncoder(MediaRecorder.OutputFormat.AMR_NB);
        mediaRecorder.setOutputFile(pathSave);
    }

    public String CreateRandomAudioFileName(int string){
        StringBuilder stringBuilder = new StringBuilder( string );
        int i = 0 ;
        while(i < string ) {
            stringBuilder.append(RandomAudioFileName.
                    charAt(random.nextInt(RandomAudioFileName.length())));

            i++ ;
        }
        return stringBuilder.toString();
    }

    private void requestPermissions()
    {
        ActivityCompat.requestPermissions(this,new String[]{
                Manifest.permission.WRITE_EXTERNAL_STORAGE,
                Manifest.permission.RECORD_AUDIO

        },  REQUEST_PERMISSION_CODE);
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, @NonNull int[] grantResults) {
        switch (requestCode)
        {
            case REQUEST_PERMISSION_CODE:
            {
                if(grantResults.length > 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED)
                    Toast.makeText(this, "Permission Garanted", Toast.LENGTH_SHORT).show();
                else
                    Toast.makeText(this, "Permission Denied", Toast.LENGTH_SHORT).show();
            }
                break;
        }
    }

    private boolean checkPermissionFromDevice() {

        int write_external_storage_result = ContextCompat.checkSelfPermission(this, Manifest.permission.WRITE_EXTERNAL_STORAGE);
        int record_audio_result = ContextCompat.checkSelfPermission(this, Manifest.permission.RECORD_AUDIO);
        return write_external_storage_result == PackageManager.PERMISSION_GRANTED &&
                record_audio_result == PackageManager.PERMISSION_GRANTED;
    }





    public void ADDRESS(){
        Intent intent = new Intent(Intent.ACTION_SEND);
        intent.setType("message/rfc882");
        intent.setData(Uri.parse("mailto:"));
        intent.putExtra(Intent.EXTRA_EMAIL, new String[]{"recipient@example.com"});
        intent.putExtra(Intent.EXTRA_SUBJECT, "Subject of Email");
        intent.putExtra(Intent.EXTRA_TEXT, "Body of Email1");
        try{
            startActivity(Intent.createChooser(intent, "Send mail..."));
        } catch (android.content.ActivityNotFoundException ex) {
            Toast.makeText(MainActivity.this, "There are no email clients installed.", Toast.LENGTH_SHORT).show();
        }
        
    }
}    
