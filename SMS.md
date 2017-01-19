# SMS

## Envio de SMS

Tenemos 2 formas de enviar SMS desde una aplicación Android (como para casi todo en Android)

* Usando una aplicación ya existente

      Intent sendIntent = new Intent(Intent.ACTION_VIEW);
      sendIntent.putExtra("sms_body", "Texto a enviar");
      sendIntent.putExtra("address", "NUMERO");
      sendIntent.setType("vnd.android-dir/mms-sms");
      startActivity(sendIntent);

* Usando nuestro própio código (https://developer.android.com/reference/android/telephony/gsm/SmsManager.html#sendTextMessage(java.lang.String, java.lang.String, java.lang.String, android.app.PendingIntent, android.app.PendingIntent))

      SmsManager smsManager = SmsManager.getDefault();
      smsManager.sendTextMessage("phoneNo", null, "sms message", null, null);

Necesitaremos el permiso android.permission.SEND_SMS


## Recepción

Utilizaremos un BackGroundReceiver, una aplicación que recibe eventos del sistema

    public class IncomingSms extends BroadcastReceiver {
        // acceso al SmsManager
        final SmsManager sms = SmsManager.getDefault();

        public void onReceive(Context context, Intent intent) {

            // Recuperamos la propiedades del intent
            final Bundle bundle = intent.getExtras();
            try {
                if (bundle != null) {

                    final Object[] pdusObj = (Object[]) bundle.get("pdus");
                    for (int i = 0; i < pdusObj.length; i++) {

          SmsMessage currentMessage = SmsMessage.createFromPdu((byte[]) pdusObj[i]);
          String phoneNumber = currentMessage.getDisplayOriginatingAddress();
          String senderNum = phoneNumber;
          String message = currentMessage.getDisplayMessageBody();


           // Show Alert
    	 int duration = Toast.LENGTH_LONG;
          Toast toast = Toast.makeText(context,
                 "senderNum: "+ senderNum + ", message: " + message, duration);
                        toast.show();

                    }
                  }
            } catch (Exception e) {
                Log.e("SmsReceiver", "Exception smsReceiver" +e);

            }
        }
    }

Y añadimos a nuestro AndroidManifest.xml


    <receiver android:name=".IncomingSms">
         <intent-filter>
             <action android:name="android.provider.Telephony.SMS_RECEIVED" />
         </intent-filter>
     </receiver>
