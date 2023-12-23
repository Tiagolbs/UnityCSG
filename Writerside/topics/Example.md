# Example

```C#
using System.Collections;
using Misc;
using Scene_Management;
using UnityEngine;

namespace Player
{
    public class PlayerController : MonoBehaviour
    {
        [SerializeField] private float _movementSpeed = 3f;
        
        [Header("Dash")]
        [SerializeField] private float _dashSpeed = 4f;
        [SerializeField] private float _dashTime = 0.2f;
        [SerializeField] private float _dashCooldown = 2f;
        [SerializeField] private TrailRenderer _trailRenderer;
        private bool _isDashing;
        private float _startingMoveSpeed;
        public bool IsFacingLeft { get; private set; }
        
        private Rigidbody2D _myRigidbody;
        public PlayerControls PlayerControls { get; private set; }
        private Vector2 _movement;
        private Animator _myAnimator;
        private SpriteRenderer _mySpriteRenderer;
        
        //Animations
        private static readonly int MoveXAnimationHash = Animator.StringToHash("moveX");
        private static readonly int MoveYAnimationHash = Animator.StringToHash("moveY");

        protected override void Awake()
        {
            base.Awake();
            PlayerControls = new PlayerControls();
            _myRigidbody = GetComponent<Rigidbody2D>();
            _myAnimator = GetComponent<Animator>();
            _mySpriteRenderer = GetComponent<SpriteRenderer>();
        }

        private void OnEnable()
        {
            PlayerControls.Enable();
        }

        private void Start()
        {
            PlayerControls.Player.Dash.performed += _ => Dash();
            _startingMoveSpeed = _movementSpeed;
            StartCoroutine(ItemUpdateRoutine());
        }

        private void Update()
        {
            
            PlayerInput();
        }

        private void FixedUpdate()
        {
            Move();
            AdjustPlayerFacingDirection();
        }

        private void PlayerInput()
        {
            _movement = PlayerControls.Player.Move.ReadValue<Vector2>();
            _myAnimator.SetFloat(MoveXAnimationHash, _movement.x);
            _myAnimator.SetFloat(MoveYAnimationHash, _movement.y);
        }
        
        private void Move()
        {
            _myRigidbody.MovePosition(_myRigidbody.position + _movement * (_movementSpeed * Time.fixedDeltaTime));
        }
        
        private void AdjustPlayerFacingDirection()
        {
            Vector2 mousePosition = PlayerControls.Player.MousePosition.ReadValue<Vector2>();
            Vector3 playerScreenPoint = UnityEngine.Camera.main.WorldToScreenPoint(transform.position);

            if (mousePosition.x < playerScreenPoint.x)
            {
                IsFacingLeft = true;
                _mySpriteRenderer.flipX = true;
            }
            else
            {
                IsFacingLeft = false;
                _mySpriteRenderer.flipX = false;
            }
        }
        
        private void Dash()
        {
            if (_isDashing)
            {
                return;
            }

            _isDashing = true;
            _movementSpeed *= _dashSpeed;
            _trailRenderer.emitting = true;
            StartCoroutine(EndDashRoutine());
        }

        private IEnumerator EndDashRoutine()
        {
            yield return new WaitForSeconds(_dashTime);
            _movementSpeed = _startingMoveSpeed;
            _trailRenderer.emitting = false;
            yield return new WaitForSeconds(_dashCooldown);
            _isDashing = false;
        }
    }
}

```