    # Android-Check-Version-App-On-Google-Play

    1) on file build.gradle: <br>
    implementation 'com.github.javiersantos:AppUpdater:2.7' <br>
    2) on MainActivity: <br>

        @Override
        protected void onResume() {
            super.onResume();
            initUpdateChecker();
        }

     // check update version in google play
        public void initUpdateChecker() {
            AppUpdaterUtils appUpdaterUtils = new AppUpdaterUtils(this)
                    .setUpdateFrom(UpdateFrom.GOOGLE_PLAY)
                    .withListener(new AppUpdaterUtils.UpdateListener() {
                        @Override
                        public void onSuccess(Update update, Boolean isUpdateAvailable) {
                            try {
                                String versionName = getPackageManager()
                                        .getPackageInfo(getPackageName(), 0).versionName;
                                if(!versionName.equals(update.getLatestVersion())) {
                                    showDialogUpdateNewVersion();
                                }
                            } catch (PackageManager.NameNotFoundException e) {
                                e.printStackTrace();
                            }
                        }

                        @Override
                        public void onFailed(AppUpdaterError error) {
                        }
                    });
            appUpdaterUtils.start();

        }

        public void showDialogUpdateNewVersion() {
            AlertDialogFragment alertDialogFragment = AlertDialogFragment.newInstance(getString(R.string.noti_new_version));
            alertDialogFragment.show(getSupportFragmentManager(), null);
            alertDialogFragment.setClickYesButtonListener(() -> {
                gotoGooglePlayForUpdateApp(MainActivity.this, MainActivity.this.getPackageName());
            });
            alertDialogFragment.setClickNoButtonListener(() -> {
                finish();
            });
        }

        public static void gotoGooglePlayForUpdateApp(Context context, String packageApp) {
            gotoGooglePlay(context, packageApp, true);
        }

        public static void gotoGooglePlay(Context context, String packageApp, boolean flagStartNewActivity) {
            if (context == null) {
                return;
            }
            try {
                Intent intent = new Intent(Intent.ACTION_VIEW, Uri.parse("market://details?id=" + packageApp));
                if (flagStartNewActivity) {
                    intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
                }
                context.startActivity(intent);
            } catch (ActivityNotFoundException e) {
                context.startActivity(new Intent(Intent.ACTION_VIEW, Uri.parse("https://play.google.com/store/apps/details?id=" + packageApp)));
            }
        }
