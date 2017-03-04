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
      
This file exists in location /data/data/com/example/app