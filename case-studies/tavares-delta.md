Tavares Delta Hub


Sistema de gestão avançado para empresas de e-commerce



Contexto

O cliente — uma consultoria financeira — tinha problemas para fazer a gestão de seus clientes por planilha, gastando horas preenchendo linhas manualmente. Também enfrentava dificuldade para extrair dados dos clientes, que frequentemente esqueciam de enviar informações importantes, demandando muito tempo do operador para cobrar e recolher esses dados.

O sistema é dividido em duas áreas: a área do admin, onde o administrador cria usuários, monta relatórios personalizados, gerencia módulos, realiza lançamentos e faz o fechamento mensal; e a área do cliente, onde ele próprio faz lançamentos e importa produtos e vendas através de planilhas e arquivos. Cada cliente pode ter até 5 empresas, todas criadas pelo administrador. O admin tem visão completa de tudo o que acontece com o cliente, além de alertas caso ele esteja esquecendo de algo — como cadastrar um fornecedor ou pagar uma fatura prestes a vencer.

O desafio técnico

O maior desafio foi desenvolver a integração de dados: um sistema em que cada módulo conversa com o outro, além da importação de dados de outras plataformas (marketplaces), que exige uma camada de normalização antes de entrar no sistema.

Outro ponto central foi a arquitetura multi-tenant. Por se tratar de uma consultoria, cada cliente pode ter múltiplas empresas, cada uma com seu próprio financeiro, estoque e métricas avançadas — exigindo isolamento de dados robusto sem multiplicar a complexidade operacional.

Decisões de arquitetura


Decisão: RLS por company_id em vez de banco por tenant
Por quê: Foi escolhida pensando em escalabilidade — manter um banco de dados separado por cliente traria custo operacional desnecessário nesta fase do projeto. Como sou o único dev, RLS por company_id também reduz a complexidade de manutenção: um único schema para migrar, monitorar e evoluir.
Decisão: SQL, RPCs e migrations aplicados apenas via Supabase MCP, com confirmação explícita antes de cada mudança; Claude Code cuida da lógica de frontend; Lovable cuida da camada visual — nenhum SQL é escrito diretamente pelo Claude Code.
Por quê: Essa decisão de processo busca extrair o máximo de cada ferramenta. O planejamento acontece em um chat que mantém histórico, conhece as regras de negócio e a arquitetura do sistema, e atua como parceiro do tech lead — delegando funções, pesquisando informações relevantes e ajudando na tomada de decisão arquitetural. O Claude Code cuida de toda a parte de código, orientado a optar por arquitetura limpa e modular. O Lovable cuida da parte visual e ajustes finos.
Decisão consciente de não fazer algo: KPIs e relatórios avançados como DRE e Curva A/B ficam restritos apenas ao admin. Pensando na experiência do cliente final, não fazia sentido construir esse tipo de módulo para quem não tem o conhecimento técnico nem o interesse em analisar esse tipo de métrica.


Um obstáculo real

Vazamento de contexto de segurança em view (security_invoker)

Onde: vw_incoming_items

Problema: A view foi criada sem WITH (security_invoker = true). No Postgres, por padrão uma view roda com os privilégios de quem a criou, não de quem está consultando — o que podia permitir que uma query via view ignorasse as políticas de RLS aplicadas nas tabelas base, potencialmente expondo dados entre tenants.

Correção: Adicionada a flag security_invoker = true, forçando a view a respeitar RLS com base no usuário que consulta.

Resultado: Virou regra permanente do projeto — toda view nova precisa dessa flag.

Resultado / estado atual

Segundo o próprio cliente, o sistema trouxe uma economia significativa de tempo, permitindo atender mais casos em menos horas de trabalho e abrindo espaço para pensar em como escalar a consultoria — algo antes limitado pela dificuldade de lidar com um volume maior de clientes.

O projeto está em reta final de desenvolvimento. A primeira versão já foi lançada e os testes iniciais foram bem-sucedidos. Os módulos core — financeiro e métricas — já estão em produção. A integração com API do Mercado Livre e outros marketplaces, incluindo a camada de normalização de dados, está finalizada. A próxima etapa é o desenvolvimento de um agente via WhatsApp que condensa as informações do sistema e apoia o tomador de decisão nas escolhas mais importantes do negócio.


Nota: nomes de clientes e dados sensíveis foram omitidos ou generalizados por confidencialidade.
