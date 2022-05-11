# moved_configuration_block

This is a guide on HOW to move your resources with the moved configuration block in Terraform. The goal is to move already created resource to different module, without destroying it,

more information [here](https://learn.hashicorp.com/tutorials/terraform/move-config#move-your-resources-with-the-moved-configuration-block).

## Requirements
Install Terraform - [instructions](https://www.terraform.io/downloads)


## Guide
1. First download the repo https://github.com/igabrpro/moved_configuration_block.git
2. Change teh directory ```cd  moved_configuration_block```
3. Execute ```terraform init``` in order to download the necessary providers
4. Execute ```terraform apply``` in order to provision the code in main.tf
5. With the code running create new directory named module ```mkdir module```
6. Create module.tf in the module folder
7. Add the following content
```
resource "random_pet" "name" {
  length    = "2"
  separator = "-"
}

output "out" {
  value = random_pet.name.id
}
```
6. Edit file main.tf located in the main directory
```
module "move_state" {
  source = "./module"
}

resource "null_resource" "hello" {
  provisioner "local-exec" {
    command = "echo Hello ${module.move_state.out}"
  }
}
```
8. Execute ```terrafrom init``` again to download the necessary plugins 
9. Add the following at the bottom of your main.tf, with this you are pointing that the ```random_pet.name (from)``` has been moved to ```module.move_state.random_pet.name(to)```

```
moved {
  from = random_pet.name
  to = module.move_state.random_pet.name
}
```

```
11. Now if you execute ```terraform apply``` you will see that there are no changest that are going to be made
```
module.move_state.random_pet.name: Refreshing state... [id=possibly-ultimately-talented-hound]
null_resource.hello: Refreshing state... [id=8094107985401697956]

Terraform will perform the following actions:
```
  # random_pet.name has moved to module.move_state.random_pet.name
    resource "random_pet" "name" {
        id        = "possibly-ultimately-talented-hound"
        # (2 unchanged attributes hidden)
    }

Plan: 0 to add, 0 to change, 0 to destroy.

```
12. Use terraform destroy to clear all resources 
