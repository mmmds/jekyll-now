---
published: false
---
Continuing analysis of application from [previous post](https://mmmds.github.io/Android-Insecure-Communication-Man-In-The-Middle-Example/) I found that sessionId is held in local file.

    private static class SessionWriterTask extends AsyncTask<Context, Void, Void>
      {
        private String sessionId;

        public WriteTask(String paramString1)
        {
          this.sessionId = paramString1;
        }

        protected Void doInBackground(Context... paramVarArgs)
        {
          try
          {
            paramVarArgs = paramVarArgs[0].openFileOutput("session.txt", 0);
            paramVarArgs.write(this.sessionId.getBytes());
            paramVarArgs.close();
            return null;
          }
          catch (IOException paramVarArgs)
          {
            Log.e("SessionWriterTask", "Cannot write", paramVarArgs);
          }
        }
      }
      
File's absolute path is /data/data/com.example.app/files/session.txt. On rooted device such file can be accessed without any problem, but on unrooted device we don't have permission to read it. I found some solution which involves making a backup and then unpacking it on computer.

$ adb backup -noapk com.example.app

Device prompt me for password for backup (on unencrypted devices it's possible to skip) and then backup.ab file appeared on my computer. 



