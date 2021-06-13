create-user:
	bash .docker-mate/utils/message.sh info "Creating user... (Make sure to use role 'Admin')"
	docker-compose exec -u application app php artisan make:user

run-import:
	bash .docker-mate/utils/message.sh info "Running GitLab import... This can take a long time..."
	docker-compose exec -u application app php artisan import:all

initial-setup:
	bash .docker-mate/utils/message.sh info "Running GitLab initial setup commands..."
	docker-compose exec -u application app composer install
	docker-compose exec -u application app php artisan key:generate
	docker-compose exec -u application app php artisan vendor:publish --provider="JeroenNoten\LaravelAdminLte\ServiceProvider" --tag=assets
	bash .docker-mate/utils/message.sh info "Running DB migrations..."
	docker-compose exec -u application app php artisan migrate
	bash .docker-mate/utils/message.sh info "Seeding DB..."
	docker-compose exec -u application app php artisan db:seed
	make create-user
	make run-import
