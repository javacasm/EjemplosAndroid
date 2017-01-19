# MMS

## Enviando MMS

Para enviar un MMS usando la aplicación ya disponible, por ejemplo enviando una imagen


    Intent sendIntent = new Intent(Intent.ACTION_SEND);
    sendIntent.setClassName("com.android.mms", "com.android.mms.ui.ComposeMessageActivity");
    sendIntent.putExtra("sms_body", "El texto del mensaje");
    sendIntent.putExtra("address", "1213123123");
    sendIntent.putExtra(Intent.EXTRA_STREAM, Uri.parse("file:///sdcard/imagen.png"));
    sendIntent.setType("image/png");
    startActivity(sendIntent);


Para enviar un vídeo, el cambio sería poco


    Intent sendIntent = new Intent(Intent.ACTION_SEND);
    sendIntent.setClassName("com.android.mms", "com.android.mms.ui.ComposeMessageActivity");
    sendIntent.putExtra("address", "1213123123");
    sendIntent.putExtra("sms_body", "El texto del mensaje");
    final File file1 = new File("filename");
    Uri uri = Uri.fromFile(file1);
    sendIntent.putExtra(Intent.EXTRA_STREAM, uri);
    sendIntent.setType("video/*");
    startActivity(sendIntent);

## Recepción de MMS

Para recibirlos usandos el mismo BroadcastReceiver, pero que ahora escuchará otros eventos

    <intent-filter>
        <action android:name="android.provider.Telephony.WAP_PUSH_RECEIVED" />
        <data android:mimeType="application/vnd.wap.mms-message" />
    </intent-filter>

El código para procesar los datos recibidos en el intent sería


    private Context mContext;
    private final String DEBUG_TAG = getClass().getSimpleName().toString();
    private static final String ACTION_MMS_RECEIVED = "android.provider.Telephony.WAP_PUSH_RECEIVED";
    private static final String MMS_DATA_TYPE = "application/vnd.wap.mms-message";

    public void receiveMMS(Intent intent)
    {
        String action = intent.getAction();
        String type = intent.getType();

        if(action.equals(ACTION_MMS_RECEIVED) && type.equals("application/vnd.wap.mms-message")){

            Bundle bundle = intent.getExtras();

            Log.d(DEBUG_TAG, "bundle " + bundle);
            SmsMessage[] msgs = null;
            String str = "";
            int contactId = -1;
            String address;

            if (bundle != null) {

                byte[] buffer = bundle.getByteArray("data");
                Log.d(DEBUG_TAG, "buffer " + buffer);
                String incomingNumber = new String(buffer);
                int indx = incomingNumber.indexOf("/TYPE");
                if(indx>0 && (indx-15)>0){
                    int newIndx = indx - 15;
                    incomingNumber = incomingNumber.substring(newIndx, indx);
                    indx = incomingNumber.indexOf("+");
                    if(indx>0){
                        incomingNumber = incomingNumber.substring(indx);
                    }
                }

                int transactionId = bundle.getInt("transactionId");
                int pduType = bundle.getInt("pduType");
                byte[] buffer2 = bundle.getByteArray("header");
                String header = new String(buffer2);

                if(contactId != -1){
                    //    showNotification(contactId, str);
                }
