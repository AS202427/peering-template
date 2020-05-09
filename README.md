# Neptune Networks Peering Config

This is the pipeline to build Neptune Networks' BGP peering configuration. Neptune uses BIRD `2.0.7` on all of its core customer routers and border routers. We rely on this pipeline to take templates and YAML and turn them into a BIRD configuration.

This BGP configuration has been built to be agnostic to Neptune Networks and should work for you as well. An example configuration file has been built for you in [`config/router.fqdn.example.yml`](config/router.fqdn.example.yml). Simply create a similar file with the relevant options to your network and execute the generation steps at the bottom of this README.

The YAML files in `config/` go through a transformation step in which they turn into `bird.conf`, `peers.conf`, and `static.conf` files in the `out/` directory.

## BGP Community Support

The BIRD configuration in this repostiory uses [BGP large communities](http://largebgpcommunities.net/) inspired by [RFC8195](https://tools.ietf.org/html/rfc8195).

### Informational

Informational BGP communities offer insight into Neptune's routing policies.

#### Origin

| Community | Description |
| --------- | ----------- |
| `YOUR_ASN:101:1` | Originated by you |
| `YOUR_ASN:101:2` | Learned from IX |
| `YOUR_ASN:101:3` | Learned from private peer |
| `YOUR_ASN:101:4` | Learned from transit provider |
| `YOUR_ASN:101:5` | Learned from customer |

#### Region

Octets in the function field are the numeric country identifier defined by ISO 3166-1.

| Community | Description |
| --------- | ----------- |
| `YOUR_ASN:102:840` | Learned in U.S. |

#### Site

| Community | Description |
| --------- | ----------- |
| `YOUR_ASN:103:1` | Learned at NY1 |

### Actionable

BGP communities which manipulate the routing policy.

#### Prepends

| Community | Description |
| --------- | ----------- |
| `YOUR_ASN:900:1` | Prepend `YOUR_ASN` once on export to all AS's |
| `YOUR_ASN:900:2` | Prepend `YOUR_ASN` twice on export to all AS's |
| `YOUR_ASN:900:3` | Prepend `YOUR_ASN` thrice on export to all AS's |
| `YOUR_ASN:991:xxxxx` | Prepend `YOUR_ASN` once on export to AS `xxxxx` |
| `YOUR_ASN:992:xxxxx` | Prepend `YOUR_ASN` once on export to AS `xxxxx` |
| `YOUR_ASN:993:xxxxx` | Prepend `YOUR_ASN` once on export to AS `xxxxx` |

#### NO_EXPORT

| Community | Description |
| --------- | ----------- |
| `YOUR_ASN:600:xxxxx` | Do not export to AS `xxxxx` |
| `YOUR_ASN:601:2` | Do not export to IX peers |
| `YOUR_ASN:601:3` | Do not export to private peers |
| `YOUR_ASN:601:4` | Do not export to transit providers |
| `YOUR_ASN:601:5` | Do not export to customers |
| `YOUR_ASN:602:840` | Do not export in U.S. |
| `YOUR_ASN:603:1` | Do not export in NY1 |

## Usage

Start by looking at `config/` and making any adjustments that are needed. Once you're ready, execute:

```
ruby lib/generator.rb
```

This will create a file for each of the templates in `/templates`, without the `.erb` suffix.

If you find that you need to make any adjustments to the templates, simply do so and then re-run the generator.

## Roadmap

- [ ] Community-based local pref
- [ ] PeeringDB max prefix support
- [ ] Blackhole support