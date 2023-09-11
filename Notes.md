Complex Terraform

I agree with all of your reasons, there is no point making the whole thing inversely more complex to save a few lines of code. You just end up spending more time maintaining fewer lines of code whereas on the other hand, more lines of code would have been straightforward to understand and maintain.

I'm seeing the bad practices of people getting too fancy in their tf. I'm from a very strong dev background, and I totally understand how fun it is to be clever, but...

You need to keep the clean code ethos of good programming and apply it to terraform. Clean code means that you understand that the computer can turn anything into functional code, but you know that people need to read and maintain it.

Pragmatic and sensible code screams intent.

My advice, don't get crazy fancy because you can. Smart uses of abstractions, dynamics, maps, sets, count should make code that solves a business problem, but also is humanly reachable. This shit is literally recipes.. don't if chicken soup, then smoke brisket this stuff..

Bad practices in Terraform have some measurable consequence, which varies from user to user. Some practices are bad almost no matter what, such as:

Creating resource modules which wrap a single resource and pass through input and output (MAYBE setting different defaults). Resources are already modular, you don't have to wrap thin in a module to make them so.

Referring to resource modules with no version, or via purely relative paths. This is acceptable when modules are internal to a manifest and are built-in, but modules maintained externally to the directory structure of a manifest can drift in content unexpectedly.

Looking from another perspective, allowing developers to write infrastructure using generic programming languages brings its own set of challenges. These challenges would be experienced by developers on a daily basis, so I'm not sure if it's the answer to the problem.

 

We have all learned entire languages or syntaxes in the past that are no longer in use. However, this does not mean that it was a waste of time. In fact, the more languages you learn, the quicker you realize that learning another one is actually much easier.

 

Personally, I am all for DSLs as long as the large provider sticks to it. I wouldn't mind writing Azure in Bicep, AWS in their DSL, GCP in YAML, or all of them with HCL where needed. What I would not be comfortable doing is having to learn C#, Go, or Rust for the sake of infrastructure deployments. I would rather learn 10x DSLs instead.

What I like about tools like bicep is the ability to literally trash all the code and start from scratch with minimal effort if needed.

With HCL you might have a guy who wanted to show off his HCL skills and complicated the HCL code in such a way that there is no way to safely upgrade simple resource and then you have to spend entire month to either reverse engineer that thing or rewrite it from scratch by importing resources and messing around with it. You are effectively becoming an HCL/Terraform engineer rather than Cloud engineer/Platform Engineer 



Complexity is in the eye of the beholder
One of the arguments I always hear when talking to people about this problem is the argument that Terraform’s DSL reduces the amount of complexity in the code because of its limited feature set. Putting aside the fact these people are arguing that the lack of features is itself a feature it doesn’t really hold up to any scrutiny.

If you do a quick search of a sufficiently complex Terraform module, you’ll see all kinds of craziness. I just picked a random module from the Terraform registry and looked at the main.tf and found this:

resource "aws_route_table_association" "redshift" {
  count = local.create_redshift_subnets && !var.enable_public_redshift ? local.len_redshift_subnets : 0

  subnet_id = element(aws_subnet.redshift[*].id, count.index)
  route_table_id = element(
    coalescelist(aws_route_table.redshift[*].id, aws_route_table.private[*].id),
    var.single_nat_gateway || var.create_redshift_subnet_route_table ? 0 : count.index,
  )
}

resource "aws_route_table_association" "redshift_public" {
  count = local.create_redshift_subnets && var.enable_public_redshift ? local.len_redshift_subnets : 0

  subnet_id = element(aws_subnet.redshift[*].id, count.index)
  route_table_id = element(
    coalescelist(aws_route_table.redshift[*].id, aws_route_table.public[*].id),
    var.single_nat_gateway || var.create_redshift_subnet_route_table ? 0 : count.index,
  )
}
Now, don’t get me wrong, I know what this is doing, and I also understand why it has to be implemented this way, but if you’re not an expert in this space, you probably have a whole bunch of reasonable questions:

Why are we using count here?
What is coalescelist?
What is element?
What is local?
And that’s just the first few lines. I’m not trying to pick on this module, I’m just trying to demonstrate that even in a language that is supposed to be “simple” and “easy to understand” you can still end up with code that is complex and difficult to understand.

Terraform’s DSL allows you to use abstractions (modules), nest resources inside those abstractions and then create varying levels of indirection when trying to instantiate resources. These things are all necessary when trying to truly create an ecosystem that allows reusability and sharing, but of course that brings with it complexity.

My personal opinion here is that when people say “it’s less complex” - what they actually mean is “I understand it”. Which is totally fine, and a reasonable position to be in, but it’s not the same thing. If you’re a “devops engineer” or “platform engineer” (or whatever the job title is called this week) and you look at the application code you’re supporting - which is all object orientated and scary and you can’t figure out where this values comes from - put yourself in the position of an application developer trying to look at YOUR Terraform code and think about their perspective. It’s just as difficult, and it was your job to learn Terraform’s DSL, the application developer has a full time job AND now you’re expecting them to learn this weird DSL too?

