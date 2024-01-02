# prisma-generate-repro

Example repo demonstrating that `yarn prisma generate` does not result in the types package being regenerated properly when the `schema.prisma` file is edited.

Running `yarn prisma generate` results in the `.prisma/client` not actually being rebuilt when the schema file has been edited. The only way to get it to rebuild is to force yarn to think that the `.prisma/client` package hasn't been installed/compiled yet.

## Steps to reproduce

- `yarn install`
- check the `StripeCustomerWhereUniqueInput` type in `node_modules/.prisma/client/index.d.ts`, and note that it requires at least one of `id` or `userEmail`.
- edit the `schema.prisma` file, and set the `StripeCustomer.email` field to be `@unique`
- run `yarn prisma generate`
- check the `StripeCustomerWhereUniqueInput` type in `node_modules/.prisma/client/index.d.ts`, and note that it still only requires at least one of `id` or `userEmail` - the `email` field should have been added as a result of adding the `@unique` tag, but wasn't.
- run `yarn rebuild @prisma/client`
- check the `StripeCustomerWhereUniqueInput` type in `node_modules/.prisma/client/index.d.ts`, and note that it now after reinstalling requires one of `id` or `userEmail` or `email`.
