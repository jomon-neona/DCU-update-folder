PATH=/sbin:/bin:/usr/sbin:/usr/bin

UPDATE_PATH="/home/root/app/software_update"
APPLICATION_UPDATE_PATH="$UPDATE_PATH/$1/update_files/application"
DRIVER_UPDATE_PATH="$UPDATE_PATH/$1/update_files/driver"
SCRIPTS_UPDATE_PATH="$UPDATE_PATH/$1/update_files/scripts"
SERVER_UPDATE_PATH="$UPDATE_PATH/$1/update_files/server"


APPLICATION_PATH="/home/root/app"
DRIVER_PATH="/home/root/app/drivers"
SCRIPTS_PATH="/home/root/app/scripts"
SERVER_PATH="/usr/tomcat/apache-tomcat-7.0.82/webapps"
APP_PID_FILE="/home/root/DCU/app_pid"
SYSTEM_REBOOT=0

printf "installer scripts started\n" > /dev/kmsg

application_update () {
	printf "application_update started\n" > /dev/kmsg
	cd $APPLICATION_UPDATE_PATH
	echo $PWD

	for file in *
	do
   	 	echo $file

   	 	rm $APPLICATION_PATH/$file
   	 	cp $APPLICATION_UPDATE_PATH/$file $APPLICATION_PATH/
	     	
		if [ -f "$CURRENT_NETWORK_FILE" ]
		then
			line=$(head -n 1 $APP_PID_FILE)
		else
			printf "app pid file not found\n" > /dev/kmsg
		fi

		app_pid=$(echo ${line} | awk '{ print $1 }')
		kill -9 $app_pid
		printf "application restarted\n" > /dev/kmsg
	done

}

driver_update () {
	printf "driver_update started\n" > /dev/kmsg
	echo "driver update"
	cd $DRIVER_UPDATE_PATH
	echo $PWD

	for file in *
	do
   		echo $file

		#rmmod $file

   		rm $DRIVER_PATH/$file
   		cp $DRIVER_UPDATE_PATH/$file $DRIVER_PATH/

		#insmod $DRIVER_PATH/$file
		
	done

	SYSTEM_REBOOT=1
	
}


scripts_update () {
	printf "scripts_update started\n" > /dev/kmsg
	echo "scripts update"
	cd $SCRIPTS_UPDATE_PATH
	echo $PWD

	for file in *
	do
   		echo $file
		
   		rm $SCRIPTS_PATH/$file
   		cp $SCRIPTS_UPDATE_PATH/$file $SCRIPTS_PATH/
	
	done

	if [ -f app_checking_deamon.sh ] || [ -f ethernet_checking.sh ]
	then
		SYSTEM_REBOOT=1
	else
		
		SYSTEM_REBOOT=0
	fi
}

server_update () {

	printf "server_update started\n" > /dev/kmsg
	
	JAVA_PATH=/usr/java/jdk1.8.0_121/bin
	TOMCAT_PATH=/usr/tomcat/apache-tomcat-7.0.82
	export PATH=$JAVA_PATH:$PATH
	export PATH=$TOMCAT_PATH/bin:$PATH
	export CATALINA_HOME=$TOMCAT_PATH
	#/bin/sh $TOMCAT_PATH/bin/shutdown.sh 
	
	printf "shutdown tomcat\n" > /dev/kmsg

	cd $SERVER_UPDATE_PATH
	echo $PWD
	
	printf "rmoving old war and replacing\n" > /dev/kmsg
	rm -rf $SERVER_PATH/DCU*
	cp DCU* $SERVER_PATH
	
	printf "starting tomcat\n" > /dev/kmsg
	#/bin/sh $TOMCAT_PATH/bin/startup.sh 
	
}
############################################################################
#checking application
checking_application () {
  	echo "checking application folder"
	if [ -z "$(ls -A $APPLICATION_UPDATE_PATH)" ]; then
	   
	   echo "application floder empty"
	else
	   echo "fond new appication updating"
	   application_update
	fi
}



#checking driver
checking_driver () {
   	echo "checking driver folder"
	if [ -z "$(ls -A $DRIVER_UPDATE_PATH)" ]; then
		printf "driver floder empty\n" > /dev/kmsg
		echo "driver floder empty"
	else
		printf "fond new driver updating\n" > /dev/kmsg
		echo "fond new driver updating"
		driver_update
	fi
}



#checking scripts
checking_scripts () {
 	echo "checking scripts folder"
	if [ -z "$(ls -A $SCRIPTS_UPDATE_PATH)" ]; then
		printf "scripts floder empty\n" > /dev/kmsg
	   	echo "scripts floder empty"
	else
		printf "fond new scripts updating\n" > /dev/kmsg
	   	echo "fond new scripts updating"
		scripts_update	
	fi
}



#checking server
checking_server () {
        echo "checking server folder"
	if [ -z "$(ls -A $SERVER_UPDATE_PATH)" ]; then
		echo "server floder empty"
	else
		echo "fond new server updating"
		server_update
	fi
}



checking_application
checking_scripts
checking_server
checking_driver

#cleaning up 
sleep 5
rm -rf $UPDATE_PATH/*



if [ SYSTEM_REBOOT == 1 ]
then
	echo "system rebooting"
	reboot
else
	echo "update completed application starting..."
fi


