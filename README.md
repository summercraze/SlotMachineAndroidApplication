# SlotMachineAndroidApplication
This is a slot machine Android Application
#There is four parts to file.
#this is the main part
package com.example.slotmachine;


import android.R.string;
import android.app.Activity;
import android.app.AlertDialog;
import android.content.DialogInterface;
import android.content.Intent;
import android.os.Bundle;
import android.view.Menu;
import android.view.MenuItem;
import android.view.View;
import android.widget.EditText;
import android.widget.ImageView;
import android.widget.LinearLayout;
import android.widget.TextView;
import android.widget.Toast;

public class GamePlay extends Activity {
	private ImageView leftImg, midImg, rightImg;
	private EditText myBet;
	private final int BAR = 1;
	private final int CHERRY = 2;
	private final int BELL = 3;
	private final int SEVEN = 4;
	private int sevensCount = 0;
	private int barsCount = 0;
	private int cherry3Count = 0;
	private int cherry2Count = 0;
	private int cherry1Count = 0;
	private int totalNumOfWins = 0;
	private int moneyYouHave = 300;
	private int[] result = new int[3];
	private AlertDialog.Builder builder;

	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.activity_game_play);
		myBet = (EditText) findViewById(R.id.editText1);
		leftImg = (ImageView) findViewById(R.id.left);
		midImg = (ImageView) findViewById(R.id.middle);
		rightImg = (ImageView) findViewById(R.id.right);
		Bundle extras = getIntent().getExtras();
		//int moneyYouHave = extras.getInt("startingMoney");
	}

	public void onGameResult(View view) {
		Intent gameResult = new Intent(GamePlay.this, GameResult.class);
		gameResult.putExtra("sevens", sevensCount);
		gameResult.putExtra("bars", barsCount);
		gameResult.putExtra("cherry3", cherry3Count);
		gameResult.putExtra("cherry2", cherry2Count);
		gameResult.putExtra("cherry1", cherry1Count);
		gameResult.putExtra("NumOfWins", totalNumOfWins);

		gameResult.putExtra("startingMoney", moneyYouHave);
		startActivity(gameResult);
	}
	
	public void onQuit(View view){
		this.finish(); 
	}
	
	public void onPull(View view) {
		String bettingMoney = myBet.getText().toString();
		if (bettingMoney.equals("")) {
			builder = new AlertDialog.Builder(GamePlay.this);
			// added strings in values folder-string.xml
			builder.setTitle(getResources().getString(R.string.dialog_title2));
			builder.setMessage(getResources()
					.getString(R.string.dialog_message2));
			builder.setCancelable(false);
			builder.setIcon(android.R.drawable.sym_def_app_icon);
			builder.setPositiveButton(
					getResources().getString(R.string.dialog_positive),
					new DialogInterface.OnClickListener() {
						public void onClick(DialogInterface dialog, int which) {
							dialog.cancel();
						}
					});
			AlertDialog dialog = builder.create();
			dialog.show();
		} else{
			try{ 
				int bettingMoneyValue = Integer.parseInt(bettingMoney);
				if (bettingMoneyValue <= 0) {
					builder = new AlertDialog.Builder(GamePlay.this);
					builder.setTitle(getResources().getString(R.string.dialog_title3));
					builder.setMessage(getResources().getString(
							R.string.dialog_message3));
					builder.setCancelable(false);
					builder.setIcon(android.R.drawable.sym_def_app_icon);
					builder.setPositiveButton(
							getResources().getString(R.string.dialog_positive),
							new DialogInterface.OnClickListener() {
								public void onClick(DialogInterface dialog, int which) {
									dialog.cancel();
								}
							});
					AlertDialog dialog = builder.create();
					dialog.show();
				} else if(bettingMoneyValue > moneyYouHave){
					builder = new AlertDialog.Builder(GamePlay.this);
					builder.setTitle(getResources().getString(R.string.dialog_title3));
					builder.setMessage(getResources().getString(
							R.string.dialog_message4));
					builder.setCancelable(false);
					builder.setIcon(android.R.drawable.sym_def_app_icon);
					builder.setPositiveButton(
							getResources().getString(R.string.dialog_positive),
							new DialogInterface.OnClickListener() {
								public void onClick(DialogInterface dialog, int which) {
									dialog.cancel();
								}
							});
					AlertDialog dialog = builder.create();
					dialog.show();
				} else {

					ImageView[] imgArr = { leftImg, midImg, rightImg };
					for (int i = 0; i < 3; i++) {
						result[i] = generateRandCode();
						switch (result[i]) {
						case 1:
							imgArr[i].setImageResource(R.drawable.bar);
							break;
						case 2:
							imgArr[i].setImageResource(R.drawable.cherry);
							break;
						case 3:
							imgArr[i].setImageResource(R.drawable.bell);
							break;
						case 4:
							imgArr[i].setImageResource(R.drawable.seven);
							break;
						default:
							break;
						}
					}
					int moneyEarned = getBet() * getWinRate(result);
					if (moneyEarned != 0) {
						Toast toast = Toast.makeText(GamePlay.this, "Your bet was $ "
								+ getBet() + ", and you won $ " + moneyEarned + ".",
								Toast.LENGTH_SHORT);
						LinearLayout toastLayout = (LinearLayout) toast.getView();
						TextView toastTV = (TextView) toastLayout.getChildAt(0);
						toastTV.setTextSize(20);
						toast.show();
						moneyYouHave += moneyEarned;
					} else {
						Toast toast = Toast.makeText(GamePlay.this, "Your bet was $ "
								+ getBet() + ", and you lost.", Toast.LENGTH_SHORT);
						LinearLayout toastLayout = (LinearLayout) toast.getView();
						TextView toastTV = (TextView) toastLayout.getChildAt(0);
						toastTV.setTextSize(20);
						toast.show();
						moneyYouHave -= getBet();
						if(moneyYouHave == 0){
							builder = new AlertDialog.Builder(GamePlay.this);
				            builder.setTitle(getResources().getString(R.string.dialog_title4));
				            builder.setMessage(getResources().getString(R.string.dialog_message5));
				            builder.setCancelable(false);
				            builder.setPositiveButton(getResources().
				                  getString(R.string.dialog_positive),new DialogInterface.OnClickListener()
				                  {
				                     @Override
				                     public void onClick(DialogInterface dialog, int which)
				                     {
				                       finish();
				                     }
				                  });
				            AlertDialog dialog = builder.create();
				            dialog.show();
						}
					}
				}
			} catch(NumberFormatException e){
				builder = new AlertDialog.Builder(GamePlay.this);
				builder.setTitle(getResources().getString(R.string.dialog_title3));
				builder.setMessage(getResources().getString(
						R.string.dialog_message3));
				builder.setCancelable(false);
				builder.setIcon(android.R.drawable.sym_def_app_icon);
				builder.setPositiveButton(
						getResources().getString(R.string.dialog_positive),
						new DialogInterface.OnClickListener() {
							public void onClick(DialogInterface dialog, int which) {
								dialog.cancel();
							}
						});
				AlertDialog dialog = builder.create();
				dialog.show();
			}
		}
	}

	public int generateRandCode() {
		double random = Math.random();
		if (random <= 0.4)
			return BAR;
		if (random <= 0.7)
			return CHERRY;
		if (random <= 0.75)
			return BELL;
		return SEVEN;
	}

	public int getBet() {
		String bet = myBet.getText().toString();
		return Integer.parseInt(bet);
	}


	public int getWinRate(int[] arr) {
		if (result[0] == SEVEN && result[1] == SEVEN && result[2] == SEVEN) {
			sevensCount++;
			totalNumOfWins++;
			return 100;
		}
		if (result[0] == BAR && result[1] == BAR && result[2] == BAR) {
			barsCount++;
			totalNumOfWins++;
			return 50;
		}
		if (result[0] == CHERRY && result[1] == CHERRY && result[2] == CHERRY) {
			cherry3Count++;
			totalNumOfWins++;
			return 30;
		}
		if (result[0] == CHERRY && result[1] == CHERRY) {
			cherry2Count++;
			totalNumOfWins++;
			return 15;
		}
		if (result[0] == CHERRY) {
			cherry1Count++;
			totalNumOfWins++;
			return 5;
		}
		return 0;
	}

	@Override
	public boolean onCreateOptionsMenu(Menu menu) {
		// Inflate the menu; this adds items to the action bar if it is present.
		getMenuInflater().inflate(R.menu.game_play, menu);
		return true;
	}

	@Override
	public boolean onOptionsItemSelected(MenuItem item) {
		// Handle action bar item clicks here. The action bar will
		// automatically handle clicks on the Home/Up button, so long
		// as you specify a parent activity in AndroidManifest.xml.
		int id = item.getItemId();
		if (id == R.id.action_settings) {
			return true;
		}
		return super.onOptionsItemSelected(item);
	}
}

